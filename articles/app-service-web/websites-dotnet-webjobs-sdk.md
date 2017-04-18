<properties 
    pageTitle="O que é o SDK do Azure WebJobs" 
    description="Uma introdução para o SDK do Azure WebJobs. Explica o que o SDK é, cenários típicos que é útil para e o código de amostras." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="what-is-the-azure-webjobs-sdk"></a>O que é o SDK do Azure WebJobs

## <a id="overview"></a>Descrição geral

Este artigo explica o que é o SDK WebJobs, revê alguns cenários comuns é útil para e fornece uma descrição geral de como utilizá-lo no seu código.

[WebJobs](websites-webjobs-resources.md) é uma funcionalidade do Azure aplicação de serviço que permite-lhe executar um programa ou script no mesmo contexto de uma aplicação web, API aplicação ou aplicação móvel. É o objetivo do [WebJobs SDK](websites-webjobs-resources.md) simplificar o código que escrever para tarefas comuns que um WebJob pode efetuar, como o processamento de imagens, processamento de fila e RSS agregação, manutenção de ficheiros e enviar e-mails. O SDK WebJobs tem funcionalidades incorporadas para trabalhar com o armazenamento do Windows Azure e Bus de serviço, para agendamento de tarefas e processamento de erros e muitas outras cenários comuns. Além disso, foi concebido para ser extensible. [WebJobs SDK está abrir origem](https://github.com/Azure/azure-webjobs-sdk/), e não existe um [repositório de origem para as extensões de abrir](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).

O SDK WebJobs inclui os seguintes componentes:

* **Pacotes de NuGet**. Pacotes de NuGet que adicionar a um projeto de aplicação de consola do Visual Studio fornecem um quadro que utiliza o seu código por decoração seus métodos com os atributos de WebJobs SDK.
  
* **Dashboard**. Parte do WebJobs SDK está incluído na aplicação de serviço de Azure e fornece monitorização avançada e diagnósticos de programas que utilizam os pacotes de NuGet. Não tem de escrever código para utilizar estas funcionalidades de monitorização e diagnósticos de.

## <a id="scenarios"></a>Cenários

Eis alguns cenários típicos que pode processar mais facilmente com o SDK do Azure WebJobs:

* Imagem do trabalho de CPU-a com um grau elevado transformação ou outros. Uma funcionalidade comum de Web sites é a capacidade de carregar imagens ou vídeos. Muitas vezes que pretende manipular o conteúdo depois de ser carregado, mas que não pretende fazer com que o utilizador Aguarde enquanto fazê-lo.

* Processamento de fila de espera. Uma forma comuns de front-end web comunicar com um serviço de back-end é utilizar filas. Quando o Web site necessita de realizar o seu trabalho,-envia uma mensagem para uma fila. Um serviço de back-end obtém mensagens de fila de espera e não o trabalho. Pode utilizar filas para processamento de imagens: por exemplo, depois do utilizador carrega um número de ficheiros, colocar os nomes de ficheiro numa mensagem de fila de espera para ser recolhida pelo processamento de back-end. Ou pode utilizar filas para melhorar a capacidade de resposta do site. Por exemplo, em vez de escrever diretamente para uma base de dados do SQL, escrever para uma fila indicar ao utilizador que tiver terminado e permita que back-end serviço alça latência alta base de dados relacional trabalhar. Para um exemplo de fila de processamento com o processo de imagem, consulte o [tutorial WebJobs SDK começar](websites-dotnet-webjobs-sdk-get-started.md).

* Agregação de RSS. Se tiver um site que mantém uma lista de RSS feeds, pode separar em todos os artigos de feeds num processo de fundo.

* Ficheiro de manutenção, como agregar ou limpar os ficheiros de registo.  Poderá ter de ficheiros de registo que está a ser criados por vários sites ou para expande-se em separado hora que pretende combinar de forma a executar tarefas de análise nos mesmos. Ou pode querer agendar uma tarefa para executar semanal para limpar os ficheiros de registo antigo.

* Penetração em tabelas Azure. Poderá ter ficheiros armazenados e blobs e pretender analisá-los e armazenar os dados em tabelas. A função de penetração poderá estar a escrever muitas filas e colunas (milhões em alguns casos) e o SDK WebJobs torna implementar facilmente esta funcionalidade. O SDK também fornece monitorização em tempo real de indicadores de progresso tal como o número de linhas escrito na tabela.

* Outras tarefas de execução longa que pretende executar num tópico de fundo, tal como [Enviar e-mails](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure). 

* Todas as tarefas que pretende executar numa agenda, tal como executar uma operação de cópia de segurança de todas as noites.

Em muitos destes cenários, poderá querer dimensionar uma aplicação web para executar no VMs vários, que seriam executado WebJobs múltiplos em simultâneo. Em alguns cenários Isto poderá resultar nos mesmos dados a ser processados várias vezes, mas este não é um problema ao utilizar a fila incorporada, blob e accionadores Bus de serviço do WebJobs SDK. O SDK assegura que as funções serão processadas apenas uma vez para cada mensagem ou blob.

O SDK WebJobs também torna mais fácil lidar cenários de processamento de erros comuns. Pode definir alertas para enviar notificações quando uma função falha e pode definir tempos limite para que uma função automaticamente é cancelada caso-não concluída dentro de um limite de tempo especificado.

## <a id="code"></a>Exemplos de código

O código de acordo com as tarefas típicas que funcionam com o armazenamento do Windows Azure é simple. Na sua aplicação de consola `Main` método cria uma `JobHost` objeto coordenadas as chamadas para métodos que escreve. A arquitetura de WebJobs SDK sabe quando os métodos de chamadas e o que os valores de parâmetro para utilizar com base nos atributos de WebJobs SDK que utilizar nos mesmos. O SDK fornece *Accionadores* que especifique o que condições fazer com que a função para ser chamado e *ligantes* que especificar como obter as informações para e terminar parâmetros do método.

Por exemplo, o atributo [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md) faz com que uma função para ser chamado quando for recebida uma mensagem numa fila de e, se o formato de mensagem for JSON para uma matriz de bytes ou um tipo de personalizado, a mensagem é automaticamente. O atributo [BlobTrigger](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) accionadores um processo sempre que é criado um novo blob numa conta de armazenamento do Windows Azure.

Eis um programa simples que uma fila de pesquisa e cria um blob para cada mensagem fila recebida:

        public static void Main()
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
        {
            writer.WriteLine(inputText);
        }

O `JobHost` objeto é um contentor para um conjunto de funções de fundo. O `JobHost` objeto monitoriza as funções, relógios para eventos que acionam-las, e executa as funções quando ocorre um evento acionador. Ligar para um `JobHost` método para indicar se pretende que o processo de contentor para executar no tópico de atual ou um tópico de fundo. No exemplo, o `RunAndBlock` método executa continuamente o processo, no tópico de atual.

Uma vez que o `ProcessQueueMessage` método neste exemplo tem um `QueueTrigger` atributo, o accionador para essa função não está a recepção de uma nova mensagem de fila de espera. O `JobHost` objeto está a observar para novas mensagens de fila de espera na fila de especificada ("webjobsqueue" neste exemplo) e quando for encontrada, chamadas `ProcessQueueMessage`. 

O `QueueTrigger` atributo enlaces a `inputText` parâmetro para o valor da mensagem fila de espera. E o `Blob` atributo enlaces um `TextWriter` objeto para um blob denominado "blobname" num contentor com o nome "containername".  

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)

A função utiliza, em seguida, estes parâmetros para escrever o valor da mensagem fila de espera para o blob:

        writer.WriteLine(inputText);

As funcionalidades de accionador e Arquivador do WebJobs SDK substancialmente simplificam o código que tem de escrever. Código feixe necessário para processar filas, blobs ou ficheiros ou para iniciar tarefas agendadas, é feito pelo framework WebJobs SDK. Por exemplo, a arquitetura cria filas que não existem ainda, abre fila de espera, lê fila mensagens, elimina fila mensagens quando processamento estiver concluído, cria contentores blob que não existem ainda, escreve blobs e assim sucessivamente.

Exemplo de código que se segue mostra uma variedade de accionadores no um WebJob: `QueueTrigger`, `FileTrigger`, `WebHookTrigger`, e `ErrorTrigger`. 

```
    public class Functions
    {
        public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        TextWriter log)
        {
            log.WriteLine(message);
        }

        public static void ProcessFileAndUploadToBlob(
            [FileTrigger(@"import\{name}", "*.*", autoDelete: true)] Stream file,
            [Blob(@"processed/{name}", FileAccess.Write)] Stream output,
            string name,
            TextWriter log)
        {
            output = file;
            file.Close();
            log.WriteLine(string.Format("Processed input file '{0}'!", name));
        }

        [Singleton]
        public static void ProcessWebHookA([WebHookTrigger] string body, TextWriter log)
        {
            log.WriteLine(string.Format("WebHookA invoked! Body: {0}", body));
        }

        public static void ProcessGitHubWebHook([WebHookTrigger] string body, TextWriter log)
        {
            dynamic issueEvent = JObject.Parse(body);
            log.WriteLine(string.Format("GitHub WebHook invoked! ('{0}', '{1}')",
                issueEvent.issue.title, issueEvent.action));
        }

        public static void ErrorMonitor(
        [ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
        [SendGrid(
            To = "admin@emailaddress.com",
            Subject = "Error!")]
         SendGridMessage message)
        {
            // log last 5 detailed errors to the Dashboard
            log.WriteLine(filter.GetDetailedMessage(5));
            message.Text = filter.GetDetailedMessage(1);
        }
    }
```

## <a id="schedule"></a>Agendamento

O `TimerTrigger` atributo fornece-lhe a capacidade para funções de accionador para executar uma agenda. Pode agendar uma WebJob como um todo através das funções individuais Azure ou agenda de um WebJob utilizando o SDK WebJobs `TimerTrigger`. Eis um exemplo de código.

```
public class Functions
{
    public static void ProcessTimer([TimerTrigger("*/15 * * * * *", RunOnStartup = true)]
    TimerInfo info, [Queue("queue")] out string message)
    {
        message = info.FormatNextOccurrences(1);
    }
}
```

Para mais de exemplo de código, consulte o artigo [TimerSamples.cs](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/ExtensionsSample/Samples/TimerSamples.cs) no repositório de azure-webjobs sdk extensões GitHub.com.

## <a name="extensibility"></a>Expansão

Que não está limitado a funcionalidade incorporada – o SDK WebJobs permite-lhe escrever accionadores personalizados e ligantes.  Por exemplo, pode escrever accionadores para eventos de cache e agendas periódicas. Um [Abrir repositório de origem](https://github.com/Azure/azure-webjobs-sdk-extensions) contiver um código de [guia detalhado sobre a expansão WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) e de exemplo para ajudar a começar a escrever o seu próprio accionadores e ligantes.

## <a id="workerrole"></a>Utilizando o SDK WebJobs fora WebJobs

Um programa que utiliza o WebJobs SDK é uma aplicação de consola padrão e pode executar qualquer lugar – -não tem de executar como um WebJob. Pode testar o programa localmente no seu computador de desenvolvimento e, em produção pode executá-lo numa função de trabalho do serviço na nuvem ou um serviço do Windows se preferir um desses ambientes. 

No entanto, o dashboard só está disponível como uma extensão de uma aplicação do Azure aplicação de serviço web. Se pretender executar fora um WebJob e continuar a utilizar o Dashboard, pode configurar uma aplicação web para utilizar a mesma conta de armazenamento que se refere a cadeia de ligação WebJobs SDK Dashboard e que WebJobs Dashboard do web app, em seguida, vai mostrar dados sobre a execução de função a partir do seu programa que está a ser executado noutro. Pode aceder do Dashboard utilizando o URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions. Para mais informações, consulte o artigo [Introdução um dashboard para desenvolvimento local com o SDK WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), mas tenha em atenção que a mensagem de blogue mostra um nome de cadeia de ligação antigo. 

## <a id="nostorage"></a>Funcionalidades de dashboard

O SDK WebJobs fornece várias vantagens mesmo que não utilize WebJobs SDK accionadores ou ligantes:

* Pode chamar funções do Dashboard.
* Pode reproduzir funções do Dashboard.
* Pode ver os registos no Dashboard de associado ao WebJob específico (registos da aplicação, escritos através da utilização Console.Out, Console.Error, rastreio, etc.) ou ligados a invocação de função específica que gerou-las (registos escritos utilizando um `TextWriter` objeto que transmite o SDK para a função como um parâmetro). 

Para mais informações, consulte o artigo [como invocar uma função manualmente](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) e [como escrever registos](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs) 

## <a id="nextsteps"></a>Próximos passos

Para mais informações sobre o SDK WebJobs, consulte o artigo [Azure WebJobs recomendado recursos](http://go.microsoft.com/fwlink/?linkid=390226).

Para obter informações sobre os melhoramentos mais recentes para o SDK WebJobs, veja as [Notas de lançamento](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes).
 
