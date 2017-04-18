<properties
    pageTitle="Monitorizar o seu APIs com gestão de Azure API, evento concentradores e Runscope"
    description="Aplicação de exemplo demonstrar a política de registo para eventhub ao Azure API gestão de conexão, Azure evento concentradores e Runscope para HTTP registo e monitorização"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="darrmi"/>

# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-runscope"></a>Monitorizar o seu APIs com gestão de Azure API, evento concentradores e Runscope

O [serviço de gestão de API](api-management-key-concepts.md) fornece várias funcionalidades para melhorar o processamento de pedidos de HTTP enviadas para o seu API HTTP. No entanto, a existência de pedidos e respostas são breves. O pedido é feito e flua através do serviço de gestão de API para sua API de back-end. A API processa o pedido e uma resposta flua trás para o consumidor API. O serviço de gestão de API mantém algumas estatísticas importantes sobre APIs para ser exibida no dashboard de portal do Publisher, mas para além que, os detalhes desaparecem.

Ao utilizar a [política](api-management-howto-policies.md) de [registo para eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) no serviço de gestão de API de pode enviar quaisquer detalhes do pedido e uma resposta a um [Concentrador de evento Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Existem várias razões pelas quais poderá pretender gerar eventos das mensagens HTTP a ser enviadas para o seu APIs. Alguns exemplos incluem de auditoria fiável de atualizações, a análise de utilização, exceção alertar e 3º integrações de terceiros.   

Este artigo demonstra como capturar a toda HTTP pedidos e respostas a mensagem, envie-a um concentrador de evento e, em seguida, essa mensagem para terceiros a um serviço que fornece HTTP registo e monitorização de serviços de reencaminhamento.

## <a name="why-send-from-api-management-service"></a>Por que motivo enviar a partir do serviço de gestão de API?
É possível escrever software intermédio HTTP que pode ligue quadros HTTP API para capturar pedidos e respostas HTTP e feed-los num registo e monitorização sistemas. A desvantagem desta abordagem é o software HTTP intermédio tem de ser integrados back-end API e tem de corresponder a plataforma da API do. Se existirem várias APIs cada uma delas tem de implementar o software intermédio. Muitas vezes existem razões porque é que não pode ser atualizado back-end APIs.

Utilizar o serviço de gestão de API do Azure para integrar com o registo de infraestrutura fornece uma solução centralizada e plataforma independente. Também é dimensionável, na parte devido as capacidades de [replicação geo](api-management-howto-deploy-multi-region.md) de gestão de API do Azure.

## <a name="why-send-to-an-azure-event-hub"></a>Por que motivo enviar a um concentrador de evento Azure?
É um razoável pedir, porque é que criar uma política que seja específica a Azure evento concentradores? Existem muitas localizações diferentes, onde poderá a pretender meu pedidos de registo. Por que motivo não enviar apenas os pedidos de diretamente para o destino final?  Que é uma opção. No entanto, quando fazer pedidos de registo a partir de um serviço de gestão de API, é necessário ter em consideração como mensagens de registo irão afetar o desempenho da API do. Poderá ser resolvidos aumentos graduais de carga, aumentando instâncias disponíveis de componentes do sistema ou tirando partido da geo replicação. No entanto, breves picos no tráfego podem causar os pedidos ser atrasada significativamente se os pedidos de infraestrutura de registo de começar a tornar mais lento em caso de carga.

Os controladores do Azure evento foi concebido para volumes de enorme penetração de dados, com capacidade para trabalhar com um número elevado extremidade dos eventos do que o número de HTTP pedidos a maior parte dos processo APIs. Centro do evento age como um tipo de memória intermédia sofisticada entre o seu serviço de gestão de API e a infraestrutura de que irá armazenar e processar as mensagens. Este procedimento assegura que o desempenho da API não irão sofrer devido a infraestrutura de registo.  

Assim que os dados foram passou a um concentrador de evento que é mantido e irá aguardar por evento concentrador os consumidores a processá-la. Centro do evento não aceita como serão processada, basta interessada nesse crucial garantir que a mensagem serão entregues com êxito.     

Evento concentradores têm a capacidade de eventos da cadeia de vários grupos do consumidor. Esta opção permite-eventos a ser processadas pelos sistemas completamente diferentes. Permite que muitos cenários de integração de suporte sem a colocar atrasos de adição no processamento do pedido API dentro do serviço de gestão de API como apenas um evento tem de ser gerado.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Uma política para enviar mensagens de aplicação/http
Um concentrador de evento aceita dados eventos como uma cadeia simple. Os conteúdos dessa cadeia são completamente por excesso para o qual. Para conseguir Compactar para cima um pedido de HTTP e enviá-la para evento concentradores precisamos de formatar a cadeia com as informações do pedido ou uma resposta. Em situações semelhante a esta, se existir um formato existente podemos pode reutilizar, em seguida, podemos poderá não ter de escrever a nossa própria código de análise. Inicialmente posso considerada utilizando o [HAR](http://www.softwareishard.com/blog/har-12-spec/) para enviar pedidos e respostas HTTP. No entanto, este formato está optimizado para armazenar uma sequência de pedidos de HTTP num formato JSON com base. Continha um número de elementos obrigatórios adicionado complexidade desnecessária para o cenário de passar a mensagem de HTTP sobre o fio.  

Foi uma opção alternativa utilizar o `application/http` tipo de multimédia, tal como descrito na especificação de HTTP [RFC 7230](http://tools.ietf.org/html/rfc7230). Este tipo de multimédia utiliza o mesmo formato exato que é utilizado para realmente enviar mensagens HTTP sobre o fio, mas a toda a mensagem pode ser colocar no corpo do pedido de HTTP outra. No nosso caso apenas vamos utilizar corpo como nossa mensagem para enviar para o evento concentradores. Convenientemente, existe um analisador de que existe em bibliotecas de [Cliente do Microsoft ASP.NET Web API 2.2](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) que podem analisar este formato e convertê-la o nativo `HttpRequestMessage` e `HttpResponseMessage` objetos.

Para poder criar esta mensagem que precisamos de tirar partido das c# com base [expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx) na gestão de API do Azure. Segue-se a política que envia uma mensagem de pedido HTTP para Azure evento concentradores.

       <log-to-eventhub logger-id="conferencelogger" partition-id="0">
       @{
           var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                       context.Request.Method,
                                                       context.Request.Url.Path + context.Request.Url.QueryString);

           var body = context.Request.Body?.As<string>(true);
           if (body != null && body.Length > 1024)
           {
               body = body.Substring(0, 1024);
           }

           var headers = context.Request.Headers
                                  .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                  .ToArray<string>();

           var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

           return "request:"   + context.Variables["message-id"] + "\n"
                               + requestLine + headerString + "\r\n" + body;
       }
       </log-to-eventhub>

### <a name="policy-declaration"></a>Declaração da política
Existem algumas coisas determinadas mencionar sobre esta expressão de política. A política de registo para eventhub tem um atributo denominado id do registo refere-se para o nome do registo que foi criado dentro do serviço de gestão de API. Podem encontrar os detalhes de como configurar um registo de evento concentrador no serviço de gestão de API no documento [como registar eventos Azure evento concentradores na gestão de API do Azure](api-management-howto-log-event-hubs.md). O atributo segundo é um parâmetro opcional que instrui concentradores eventos que criar a partições para armazenar a mensagem no. Evento concentradores utilizam a partições para ativar scalabilty e requer um mínimo de dois. A entrega de mensagens e ordenada é apenas se garante dentro de uma partição. Se não podemos indique concentrador de evento na qual partição para colocar a mensagem, irá utilizar um algoritmo de round robin para distribuir a carga. No entanto, que podem causar algumas das nossas mensagens para serem processados fora de ordem.  

### <a name="partitions"></a>Partições
Para assegurar que as nossas mensagens são entregues para os consumidores por ordem e tirar partido das capacidades de distribuição de carga de partições, que escolhi enviar mensagens de pedido HTTP para uma partição de mensagens e de HTTP resposta a uma partição de segunda. Isto irá garantir uma distribuição de carga par e podemos pode garantir que todos os pedidos de serão consumidos por ordem e todas as respostas serão consumidas por ordem. É possível para uma resposta a consumidas antes do pedido correspondente, mas à medida que não é um problema temos um mecanismo diferentes para correlacionar os pedidos para respostas e podemos saber que pedidos antecedem sempre as respostas.

### <a name="http-payloads"></a>HTTP payloads
Depois de criar o `requestLine` podemos verificar para ver se o corpo do pedido deve ser truncado. Corpo do pedido é truncado para único 1024. Isto pode ser aumentado, no entanto, mensagens de evento concentrador individuais estão limitadas a 256KB, por isso, é provável que algumas mensagens HTTP organismos irá não se ajusta numa única mensagem. Quando efetuar a funcionalidade de registo e a análise de uma quantidade significativa das informações pode ser derivada apenas a linha de pedido de HTTP e cabeçalhos. Além disso, os pedidos de API muitos apenas devolvem pequenos organismos e por isso, a perda de valor informações por truncar organismos grandes é bastante mínima em comparação com a redução de transferência, transformação e os custos de armazenamento para manter todos os conteúdos de corpo. Uma nota final sobre o corpo de processamento é que precisamos passar `true` para a como<string>método () porque são de leitura do conteúdo do corpo, mas foi também pretende o API possam ler o corpo de back-end. Por passagem TRUE para este método Vamos fazer com que o corpo ser colocada na memória intermédia para que possa ler uma segunda vez. Isto é importante ter em consideração se tiver uma API que não carregar ficheiros grandes ou utiliza inquéritos longo. Nestes casos seria melhor evitar de todo o corpo de leitura.   

### <a name="http-headers"></a>Cabeçalhos de HTTP
Cabeçalhos de HTTP podem ser transferidos simplesmente ao longo para o formato da mensagem num formato de par valor/chave simples. Vamos ter escolhido exclua determinados campos sensíveis a maiúsculas e de segurança, para evitar desnecessariamente fugas informações de credenciais. Não é provável que teclas API e outras credenciais seriam utilizadas para fins de análise. Se pretender que recomendamos efetuar análises do utilizador e o produto específico que estão a utilizar, em seguida, podemos foi possível obter que a partir do `context` objeto e adicionar essa informação à mensagem.     
### <a name="message-metadata"></a>Mensagem de metadados
Quando criar a mensagem completa para enviar para o concentrador de evento, a primeira linha não é realmente para parte do `application/http` mensagem. A primeira linha é metadados adicionais que consiste se a mensagem é um pedido de ou mensagem de resposta e um id da mensagem que é utilizado para correlacionar pedidos para as respostas. O id da mensagem é criado utilizando a política de outra que tem este aspeto:

    <set-variable name="message-id" value="@(Guid.NewGuid())" />

Pedimos poderia ter criado a mensagem de pedido, armazenado que uma variável até a resposta foi devolvida e, em seguida, basta enviada pedidos e respostas como uma única mensagem. No entanto, enviar pedidos e respostas de forma independente e utilizando um id de mensagens para correlacionar as duas, recebemos um pouco mais flexibilidade no tamanho da mensagem, a capacidade de tirar partido das várias partições, embora manutenção de encomenda de mensagem e o pedido aparecerá no nosso dashboard registo mais cedo. Também poderá alguns cenários onde uma resposta válida nunca é enviada para o centro do evento, possivelmente devido a um erro de pedido fatal no serviço de gestão de API, mas ainda será temos um registo do pedido.

A política para enviar a mensagem HTTP resposta é muito semelhante ao pedido de e para a configuração da política completa tem o seguinte aspeto:

      <policies>
        <inbound>
            <set-variable name="message-id" value="@(Guid.NewGuid())" />
            <log-to-eventhub logger-id="conferencelogger" partition-id="0">
              @{
                  var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                              context.Request.Method,
                                                              context.Request.Url.Path + context.Request.Url.QueryString);

                  var body = context.Request.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Request.Headers
                                       .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                       .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                       .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "request:"   + context.Variables["message-id"] + "\n"
                                      + requestLine + headerString + "\r\n" + body;
              }
          </log-to-eventhub>
        </inbound>
        <backend>
            <forward-request follow-redirects="true" />
        </backend>
        <outbound>
            <log-to-eventhub logger-id="conferencelogger" partition-id="1">
              @{
                  var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                                      context.Response.StatusCode,
                                                      context.Response.StatusReason);

                  var body = context.Response.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Response.Headers
                                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                                  .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "response:"  + context.Variables["message-id"] + "\n"
                                      + statusLine + headerString + "\r\n" + body;
             }
          </log-to-eventhub>
        </outbound>
      </policies>

O `set-variable` política cria um valor que esteja acessível por ambos os `log-to-eventhub` política na `<inbound>` secção e o `<outbound>` secção.  

## <a name="receiving-events-from-event-hubs"></a>Receber eventos do evento concentradores
Eventos a partir do Azure evento Hub são recebidos através do [protocolo AMQP](http://www.amqp.org/). A equipa do Microsoft serviço Bus efetuadas cliente bibliotecas disponíveis para facilitar os eventos consome. Existem duas abordagens diferentes suportadas, um está a ser um *Consumidor direta* e o outro estiver a utilizar o `EventProcessorHost` escolares. Exemplos destas duas abordagens podem encontrar no [Guia de programação de concentradores do evento](../event-hubs/event-hubs-programming-guide.md). A versão abreviada das diferenças for, `Direct Consumer` dá-lhe controlo total e o `EventProcessorHost` faz algum do trabalho canalização para que faz, mas algumas suposições sobre como processará esses eventos.  

### <a name="eventprocessorhost"></a>EventProcessorHost
Neste exemplo, vamos utilizar a `EventProcessorHost` para simplificar, no entanto-poderá não a melhor escolha para este cenário em particular. `EventProcessorHost`é que o disco rígido trabalho certificar-se de que não tem de se preocupar com threads problemas dentro de uma classe de processador de evento específico. No entanto, no nosso cenário, podemos são simplesmente converter a mensagem para outro formato de e transmitir ao longo para outro serviço utilizando um método de assíncrona. Não é necessário para atualizar o estado partilhado e, consequentemente, não risco de threads problemas. Para a maioria dos cenários, `EventProcessorHost` provavelmente é a melhor escolha e certamente é a opção mais fácil.     

### <a name="ieventprocessor"></a>IEventProcessor
O conceito central ao utilizar `EventProcessorHost` é criar uma uma implementação da `IEventProcessor` interface que contém o método `ProcessEventAsync`. A essência desse método é mostrada aqui:

  assíncrona {IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages) tarefa

           foreach (EventData eventData in messages)
           {
               _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

               try
               {
                   var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
                   await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
               }
               catch (Exception ex)
               {
                   _Logger.LogError(ex.Message);
               }
           }
            ... checkpointing code snipped ...
        }

Uma lista de objetos EventData são transmitidos para o método e podemos iteramos sobre essa lista. Bytes de cada método são analisados para um objeto de HttpMessage e objeto lhe é transmitido uma instância da IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
O `HttpMessage` instância contém três partes de dados:

      public class HttpMessage
       {
           public Guid MessageId { get; set; }
           public bool IsRequest { get; set; }
           public HttpRequestMessage HttpRequestMessage { get; set; }
           public HttpResponseMessage HttpResponseMessage { get; set; }

        ... parsing code snipped ...

      }

O `HttpMessage` instância contém um `MessageId` GUID nos ligar-se o pedido de HTTP a resposta de HTTP correspondente e um valor booleano que identifica se o objeto contiver uma instância de um HttpRequestMessage e HttpResponseMessage permite. Ao utilizar incorporado na classes HTTP a partir de `System.Net.Http`, posso conseguiram tirar partido da `application/http` analisar o código que vem incluído no `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
O `HttpMessage` instância, em seguida, seja reencaminhada para implementação do `IHttpMessageProcessor` que é uma interface que criei para separar o de recepção e interpretação do evento a partir do Azure evento Hub e o processamento real do mesmo.


## <a name="forwarding-the-http-message"></a>Reencaminhar a mensagem HTTP
Neste exemplo, posso decidiu seria interessante para transmitir o pedido de HTTP sobre para [Runscope](http://www.runscope.com). Runscope é um serviço baseado na nuvem que especializada em HTTP depuração, registo e monitorização. Possuem uma camada gratuita, para que seja mais fácil experimentar e permitir-nos ver os pedidos HTTP fluir em tempo real através da nossa o serviço de gestão de API.

O `IHttpMessageProcessor` pós-implementação tiverem este aspeto,

      public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
       {
           private HttpClient _HttpClient;
           private ILogger _Logger;
           private string _BucketKey;
           public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
           {
               _HttpClient = httpClient;
               var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
               _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
               _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
               _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
               _Logger = logger;
           }

           public async Task ProcessHttpMessage(HttpMessage message)
           {
               var runscopeMessage = new RunscopeMessage()
               {
                   UniqueIdentifier = message.MessageId
               };

               if (message.IsRequest)
               {
                   _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
                   runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
               }
               else
               {
                   _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
                   runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
               }

               var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
               messagesLink.BucketKey = _BucketKey;
               messagesLink.RunscopeMessage = runscopeMessage;
               var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
               _Logger.LogDebug("Request sent to Runscope");
           }
       }

Posso conseguiram tirar partido de uma [biblioteca de cliente existente para o Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha) torna mais fácil push `HttpRequestMessage` e `HttpResponseMessage` instâncias o respetivo serviço. Para poder aceder a API Runscope terá uma conta e uma tecla de API. Instruções para obter uma chave de API podem ser encontradas na screencast a [Criação de aplicações do Access Runscope API](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api) .

## <a name="complete-sample"></a>Exemplo completos
O [código de origem](https://github.com/darrelmiller/ApimEventProcessor) e testes de amostra estão incluídos na Github. Precisar de um [Serviço de gestão de API](api-management-get-started.md), [Um concentrador de evento ligada](api-management-howto-log-event-hubs.md)e uma [Conta de armazenamento](../storage/storage-create-storage-account.md) para executar o exemplo para si.   

A amostra é apenas uma simple aplicação de consola que recebe para eventos que vão a partir do Hub do evento, converte-los para um `HttpRequestMessage` e `HttpResponseMessage` objetos e, em seguida, reencaminhe-os para a API Runscope.

Na seguinte imagem animada, pode ver um pedido para uma API no Portal do programador, a aplicação da consola a mostrar a mensagem a ser recebeu, processadas e transmitidas e, em seguida, o pedido e uma resposta a aparecer no inspector de tráfego de Runscope.

![Demonstração do pedido a ser reencaminhada para Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Resumo
Serviço de gestão de API Azure fornece um local ideal para capturar o tráfego HTTP em viagem de e para a sua APIs. Azure evento concentradores é uma solução altamente dimensionável, baixo custo para capturar que o tráfego e alimentação-lo na sistemas de processamento secundário para o registo, monitorização e outros sofisticados e análise. Ligar para o tráfego de festa 3º sistemas aspeto, Runscope uma alteração simples como algumas linhas de seis do código de monitorização.

## <a name="next-steps"></a>Próximos passos
-   Saiba mais sobre Azure evento concentradores
    -   [Introdução ao Azure evento concentradores](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [Receber mensagens com EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [Guia de programação de concentradores de evento](../event-hubs/event-hubs-programming-guide.md)
-   Saiba mais sobre a integração de gestão de API e concentradores de evento
    -   [Como iniciar sessão eventos Azure evento concentradores na gestão de API do Azure](api-management-howto-log-event-hubs.md)
    -   [Referência da entidade de registo](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [referência da política de registo para eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    