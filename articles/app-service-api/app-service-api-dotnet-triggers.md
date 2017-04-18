<properties 
    pageTitle="Aplicação de serviço API aplicação accionadores | Microsoft Azure" 
    description="Como implementar o accionadores numa aplicação do API na aplicação de serviço do Azure" 
    services="logic-apps" 
    documentationCenter=".net" 
    authors="guangyang"
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="na" 
    ms.tgt_pltfrm="dotnet" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="rachelap"/>

# <a name="azure-app-service-api-app-triggers"></a>Azure Accionadores da aplicação API do serviço de aplicação

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2014-12-01-pré-visualização de aplicações de API.


## <a name="overview"></a>Descrição geral

Este artigo explica como implementar API aplicação accionadores e consuma-los a partir de uma aplicação de lógica.

Todos os de fragmentos de código neste tópico são copiados do [exemplo de código FileWatcher API aplicação](http://go.microsoft.com/fwlink/?LinkId=534802). 

Tenha em atenção que terá de transferir o pacote de nuget seguinte para o código neste artigo para criar e executar: [http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/).

## <a name="what-are-api-app-triggers"></a>O que são API aplicação accionadores?

É um cenário comum para uma aplicação de API seja acionada um evento para que os clientes da aplicação API podem tomar a acção apropriada em resposta ao evento. O mecanismo REST API com base no que suporte este cenário chama um acionador de aplicação API. 

Por exemplo, digamos que o código do cliente está a utilizar a [aplicação do Twitter API de conexão](../app-service-logic/app-service-logic-connector-twitter.md) e seu código precisa executar uma ação com base na novas tweets que contêm palavras específicas. Neste caso, poderá definir um accionador inquéritos ou push para facilitar a esta necessidade.

## <a name="poll-trigger-versus-push-trigger"></a>Acionar um inquérito versus accionador de emissão

Neste momento, dois tipos de accionadores são suportados:

- Acionar um inquérito - cliente pesquisa a aplicação de API para a notificação de um evento ter sido chama 
- Push accionador - cliente é notificado pela aplicação do API quando é acionada um evento 

### <a name="poll-trigger"></a>Acionador de inquérito

Um acionador de inquérito é implementado como um REST API normal e espera os seus clientes (tal como uma aplicação de lógica) para consultá-lo para poder obter a notificação. Enquanto o cliente pode manter o estado, o accionador inquérito propriamente dito é sem estado. 

As seguintes informações em relação aos pacotes de pedidos e respostas ilustram alguns aspetos chaves do contrato de accionador inquérito:

- Pedido
    - Método de HTTP: obter
    - Parâmetros
        - triggerState - este parâmetro opcional permite que clientes especificar o seu estado para que o accionador inquérito corretamente pode decidir se pretende voltar a notificação ou não com base no estado de especificado.
        - Parâmetros API específicos
- Resposta
    - Estado código **200** - pedido é válida e existe uma notificação a partir do accionador. O conteúdo da notificação de será o corpo da resposta. Um cabeçalho de "Repetir-após" na resposta indica que dados adicionais notificação tem de ser obtidos com uma chamada do pedido subsequentes.
    - Estado código **202** - pedido é válido, mas não existe nenhuma notificação de novo a partir do accionador.
    - Estado código **4xx** - pedido não é válido. O cliente não deve repetir o pedido.
    - Estado código **5xx** - pedido resultou um erro de servidor interno e/ou um problema temporário. O cliente deve repetir o pedido.

O fragmento de código seguinte é um exemplo de como implementar um acionador de inquérito.

    // Implement a poll trigger.
    [HttpGet]
    [Route("api/files/poll/TouchedFiles")]
    public HttpResponseMessage TouchedFilesPollTrigger(
        // triggerState is a UTC timestamp
        string triggerState,
        // Additional parameters
        string searchPattern = "*")
    {
        // Check to see whether there is any file touched after the timestamp.
        var lastTriggerTimeUtc = DateTime.Parse(triggerState).ToUniversalTime();
        var touchedFiles = Directory.EnumerateFiles(rootPath, searchPattern, SearchOption.AllDirectories)
            .Select(f => FileInfoWrapper.FromFileInfo(new FileInfo(f)))
            .Where(fi => fi.LastAccessTimeUtc > lastTriggerTimeUtc);

        // If there are files touched after the timestamp, return their information.
        if (touchedFiles != null && touchedFiles.Count() != 0)
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventTriggered(new { files = touchedFiles });
        }
        // If there are no files touched after the timestamp, tell the caller to poll again after 1 mintue.
        else
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventWaitPoll(new TimeSpan(0, 1, 0));
        }
    }

Para testar este accionador de inquérito, siga estes passos:

1. Implemente a aplicação de API com uma definição de autenticação do **público anónimo**.
2. Ligar a operação **de toque** para um ficheiro de toque. A imagem seguinte mostra um pedido de exemplo através do Postman.
   ![Chamada Tátil operação através do Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
3. Ligar ao accionador inquérito com o parâmetro de **triggerState** definido para um carimbo de data / hora anterior ao passo #2. A imagem seguinte mostra o pedido de exemplo através do Postman.
   ![Chamada inquérito accionador através do Postman](./media/app-service-api-dotnet-triggers/callpolltriggerfrompostman.PNG)

### <a name="push-trigger"></a>Notificações push accionador

Um accionador push é implementado como um REST API normal emite notificações para clientes que tenham registou para ser notificado quando fogo eventos específicos.

As seguintes informações em relação aos pacotes de pedidos e respostas ilustram alguns aspetos chaves do contrato de accionador push.

- Pedido
    - Método de HTTP: colocar
    - Parâmetros
        - triggerId: obrigatório - opaca cadeia de texto (tal como um GUID) que representa o registo de um accionador push.
        - callbackUrl: obrigatório - URL da chamada de retorno invocar quando o evento é despoletado. A invocação for uma chamada de mensagem HTTP simple.
        - Parâmetros API específicos
- Resposta
    - Estado código **200** - pedido para registar o cliente com êxito.
    - Estado código **4xx** - pedido não é válido. O cliente não deve repetir o pedido.
    - Estado código **5xx** - pedido resultou um erro de servidor interno e/ou um problema temporário. O cliente deve repetir o pedido.
- Chamada de retorno
    - Método de HTTP: publicar
    - Pedir corpo: conteúdo de notificação.

O fragmento de código seguinte é um exemplo de como implementar um accionador push:

    // Implement a push trigger.
    [HttpPut]
    [Route("api/files/push/TouchedFiles/{triggerId}")]
    public HttpResponseMessage TouchedFilesPushTrigger(
        // triggerId is an opaque string.
        string triggerId,
        // A helper class provided by the AppService service SDK.
        // Here it defines the input of the push trigger is a string and the output to the callback is a FileInfoWrapper object.
        [FromBody]TriggerInput<string, FileInfoWrapper> triggerInput)
    {
        // Register the trigger to some trigger store.
        triggerStore.RegisterTrigger(triggerId, rootPath, triggerInput);

        // Extension method provided by the AppService service SDK indicating the registration is completed.
        return this.Request.PushTriggerRegistered(triggerInput.GetCallback());
    }

    // A simple in-memory trigger store.
    public class InMemoryTriggerStore
    {
        private static InMemoryTriggerStore instance;

        private IDictionary<string, FileSystemWatcher> _store;

        private InMemoryTriggerStore()
        {
            _store = new Dictionary<string, FileSystemWatcher>();
        }

        public static InMemoryTriggerStore Instance
        {
            get
            {
                if (instance == null)
                {
                    instance = new InMemoryTriggerStore();
                }
                return instance;
            }
        }

        // Register a push trigger.
        public void RegisterTrigger(string triggerId, string rootPath,
            TriggerInput<string, FileInfoWrapper> triggerInput)
        {
            // Use FileSystemWatcher to listen to file change event.
            var filter = string.IsNullOrEmpty(triggerInput.inputs) ? "*" : triggerInput.inputs;
            var watcher = new FileSystemWatcher(rootPath, filter);
            watcher.IncludeSubdirectories = true;
            watcher.EnableRaisingEvents = true;
            watcher.NotifyFilter = NotifyFilters.LastAccess;

            // When some file is changed, fire the push trigger.
            watcher.Changed +=
                (sender, e) => watcher_Changed(sender, e,
                    Runtime.FromAppSettings(),
                    triggerInput.GetCallback());

            // Assoicate the FileSystemWatcher object with the triggerId.
            _store[triggerId] = watcher;

        }

        // Fire the assoicated push trigger when some file is changed.
        void watcher_Changed(object sender, FileSystemEventArgs e,
            // AppService runtime object needed to invoke the callback.
            Runtime runtime,
            // The callback to invoke.
            ClientTriggerCallback<FileInfoWrapper> callback)
        {
            // Helper method provided by AppService service SDK to invoke a push trigger callback.
            callback.InvokeAsync(runtime, FileInfoWrapper.FromFileInfo(new FileInfo(e.FullPath)));
        }
    }

Para testar este accionador de inquérito, siga estes passos:

1. Implemente a aplicação de API com uma definição de autenticação do **público anónimo**.
2. Navegue até à [http://requestb.in/](http://requestb.in/) para criar um RequestBin que irá servir como o URL da sua chamada de retorno.
3. Ligar ao accionador push com um GUID como **triggerId** e o URL de RequestBin como **callbackUrl**.
   ![Chamada Push accionador através do Postman](./media/app-service-api-dotnet-triggers/callpushtriggerfrompostman.PNG)
4. Ligar a operação **de toque** para um ficheiro de toque. A imagem seguinte mostra um pedido de exemplo através do Postman.
   ![Chamada Tátil operação através do Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
5. Verifique o RequestBin para confirmar que a chamada de retorno do push accionador é chamada com a saída de propriedade.
   ![Chamada inquérito accionador através do Postman](./media/app-service-api-dotnet-triggers/pushtriggercallbackinrequestbin.PNG)

### <a name="describe-triggers-in-api-definition"></a>Descrever accionadores na definição de API

Depois de implementar os accionadores e implementar a sua aplicação de API para Azure, navegue para a **Definição de API** pá no portal do Azure pré-visualizar e verá que accionadores são automaticamente reconhecidos na IU do qual está condicionada pela definição de Swagger 2.0 API da aplicação API.

![API definição pá](./media/app-service-api-dotnet-triggers/apidefinitionblade.PNG)

Se clicar no botão **Transferir Swagger** e abra o ficheiro JSON, verá os resultados semelhantes ao seguinte:

    "/api/files/poll/TouchedFiles": {
      "get": {
        "operationId": "Files_TouchedFilesPollTrigger",
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    },
    "/api/files/push/TouchedFiles/{triggerId}": {
      "put": {
        "operationId": "Files_TouchedFilesPushTrigger",
        ...
        "x-ms-scheduler-trigger": "push"
      }
    }

A propriedade de extensão **x-ms-schedular-accionador** é como accionadores são descritos na definição de API e é adicionado automaticamente ao gateway aplicação API quando pedir a definição de API através do gateway se o pedido para um dos seguintes critérios. (Também pode adicionar esta propriedade manualmente.)

- Acionador de inquérito
    - Se o método HTTP está a **obter**.
    - Se a propriedade **IDOperação** contém a cadeia **acionador**.
    - Se a propriedade **parâmetros** inclui um parâmetro com uma propriedade de **nome** definida para **triggerState**.
- Notificações push accionador
    - Se o método HTTP está **colocar**.
    - Se a propriedade **IDOperação** contém a cadeia **acionador**.
    - Se a propriedade **parâmetros** inclui um parâmetro com uma propriedade de **nome** definida para **triggerId**.

## <a name="use-api-app-triggers-in-logic-apps"></a>Utilizar accionadores da aplicação API nas aplicações de lógica

### <a name="list-and-configure-api-app-triggers-in-the-logic-apps-designer"></a>Listar e configurar accionadores da aplicação API no estruturador de aplicações de lógica

Se criar uma aplicação de lógica no mesmo grupo de recursos, como a aplicação de API, poderá conseguir adicioná-lo à tela de estruturador simplesmente clicando na mesma. As seguintes imagens mostram isto:

![Accionadores no Designer de aplicação de lógica](./media/app-service-api-dotnet-triggers/triggersinlogicappdesigner.PNG)

![Configurar o inquérito accionador no Designer de aplicação de lógica](./media/app-service-api-dotnet-triggers/configurepolltriggerinlogicappdesigner.PNG)

![Configurar Push accionador no Designer de aplicação de lógica](./media/app-service-api-dotnet-triggers/configurepushtriggerinlogicappdesigner.PNG)

## <a name="optimize-api-app-triggers-for-logic-apps"></a>Otimizar accionadores de aplicação de API para as aplicações de lógica

Depois de adicionar accionadores para uma aplicação de API, existem algumas coisas que pode fazer para melhorar a experiência quando utilizar a aplicação de API numa aplicação lógica.

Por exemplo, o parâmetro de **triggerState** para accionadores inquérito deve ser definido com a seguinte expressão na aplicação lógica. Esta expressão deve avaliar a última invocação de accionador a partir da aplicação de lógica e esse valor devolvido.  

    @coalesce(triggers()?.outputs?.body?['triggerState'], '')

Nota: Para obter uma explicação sobre as funções utilizadas na expressão acima, consulte a documentação sobre a [Linguagem de definição de fluxo de trabalho de aplicação de lógica](https://msdn.microsoft.com/library/azure/dn948512.aspx).

Utilizadores da aplicação lógica seriam necessário fornecer a expressão acima para o parâmetro **triggerState** ao utilizar o accionador. É possível para que este valor pré-configurar pelo estruturador da aplicação de lógica através da propriedade de extensão **x-ms scheduler recomendação**.  A propriedade de extensão de **visibilidade do ms x** pode ser definida para um valor de *interna* para que o parâmetro propriamente dito não é apresentado no estruturador de.  O fragmento de seguinte ilustra que.

    "/api/Messages/poll": {
      "get": {
        "operationId": "Messages_NewMessageTrigger",
        "parameters": [
          {
            "name": "triggerState",
            "in": "query",
            "required": true,
            "x-ms-visibility": "internal",
            "x-ms-scheduler-recommendation": "@coalesce(triggers()?.outputs?.body?['triggerState'], '')",
            "type": "string"
          }
        ]
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    }

Para accionadores push, o parâmetro **triggerId** tem de identificar exclusivamente a aplicação de lógica. É uma melhor prática recomendada definir esta propriedade para o nome do fluxo de trabalho utilizando a seguinte expressão:

    @workflow().name

Utilizar as propriedades de extensão **x-ms scheduler recomendação** e **visibilidade do ms x** na sua definição de API, a aplicação de API transmitir ao estruturador de aplicação de lógica para definir automaticamente esta expressão para o utilizador.

        "parameters":[  
          {  
            "name":"triggerId",
            "in":"path",
            "required":true,
            "x-ms-visibility":"internal",
            "x-ms-scheduler-recommendation":"@workflow().name",
            "type":"string"
          },


### <a name="add-extension-properties-in-api-defintion"></a>Adicionar propriedades da extensão na definição de API

Informações adicionais metadados - como a extensão propriedades **x-ms scheduler recomendação** e **visibilidade do ms x** - podem ser adicionadas na definição de API numa de duas formas: estática ou dinâmica.

Para metadados estático, pode editar o ficheiro de */metadata/apiDefinition.swagger.json* no seu projeto e adicionar manualmente as propriedades diretamente.

Para as aplicações do API usar metadados dinâmico, pode editar o ficheiro SwaggerConfig.cs para adicionar um filtro de operação que pode adicionar estes extensões.

    GlobalConfiguration.Configuration 
        .EnableSwagger(c =>
            {
                ...
                c.OperationFilter<TriggerStateFilter>();
                ...
            }


Segue-se um exemplo de como esta classe pode ser implementada para facilitar o cenário de metadados dinâmicos.

    // Add extension properties on the triggerState parameter
    public class TriggerStateFilter : IOperationFilter
    {

        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
        {
            if (operation.operationId.IndexOf("Trigger", StringComparison.InvariantCultureIgnoreCase) >= 0)
            {
                // this is a possible trigger
                var triggerStateParam = operation.parameters.FirstOrDefault(x => x.name.Equals("triggerState"));
                if (triggerStateParam != null)
                {
                    if (triggerStateParam.vendorExtensions == null)
                    {
                        triggerStateParam.vendorExtensions = new Dictionary<string, object>();
                    }

                    // add 2 vendor extensions
                    // x-ms-visibility: set to 'internal' to signify this is an internal field
                    // x-ms-scheduler-recommendation: set to a value that logic app can use
                    triggerStateParam.vendorExtensions.Add("x-ms-visibility", "internal");
                    triggerStateParam.vendorExtensions.Add("x-ms-scheduler-recommendation",
                                                           "@coalesce(triggers()?.outputs?.body?['triggerState'], '')");
                }
            }
        }
    }
 
