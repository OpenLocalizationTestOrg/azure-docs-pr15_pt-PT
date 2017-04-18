<properties
   pageTitle="Otimizar o seu código Azure no Visual Studio | Microsoft Azure"
   description="Saiba mais sobre como Azure código ferramentas de otimização no Visual Studio ajudam a tornar o seu código mais robustas e melhor desempenho."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="optimizing-your-azure-code"></a>Otimizar o seu código Azure

Quando estiver a programação as aplicações que utilizam Microsoft Azure, existem algumas das práticas coding que deve seguir para ajudar a evitar problemas com a aplicação escalabilidade, o comportamento e o desempenho num ambiente na nuvem. A Microsoft fornece uma ferramenta de análise de código do Azure que reconhece e identifica várias destes problemas frequentemente encontrados e ajuda-o a resolvê-los. Pode transferir a ferramenta no Visual Studio através do NuGet.

## <a name="azure-code-analysis-rules"></a>Azure regras de análise de código

A ferramenta de análise de código do Azure utiliza as seguintes regras para sinalizar automaticamente o seu código Azure quando encontrar problemas conhecidos que afetam o desempenho. Detetados problemas são apresentadas como um avisos ou compilador erros. Correções de código ou sugestões para resolver o aviso ou erro muitas vezes são fornecidas através de um ícone de lâmpada.

## <a name="avoid-using-default-in-process-session-state-mode"></a>Evitar utilizar o modo de estado de sessão (no processo) predefinido

### <a name="id"></a>ID

AP0000

### <a name="description"></a>Descrição

Se utilizar o modo de estado de sessão (no processo) predefinido para aplicações na nuvem, pode perder o estado da sessão.

Partilhe as suas ideias e feedback na [análise de código do Azure comentários](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Por predefinição, o modo de estado de sessão especificado no ficheiro Web. config é no processo. Além disso, se nenhuma entrada especificada no ficheiro de configuração, o modo de estado da sessão assume a predefinição no processo. O modo no processo de armazena o estado da sessão na memória no servidor web. Quando uma instância for reiniciada ou uma nova instância é utilizada para balanceamento de carga ou suporte de mudança, não é guardado o estado da sessão armazenado na memória no servidor web. Esta situação impede que a aplicação a ser dimensionáveis na nuvem.

Estado da sessão ASP.NET suporta várias opções de armazenamento diferente para os dados de estado de sessão: InProc, StateServer, SQLServer, personalizado e desativado. É recomendável que utilize o modo de personalizado para alojar dados num arquivo de estado da sessão externo, como o [fornecedor de estado da sessão Azure para Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### <a name="solution"></a>Solução

É uma solução recomendada armazenar o estado da sessão num serviço de cache gerida. Saiba como utilizar o [fornecedor de estado da sessão Azure para Redis](http://go.microsoft.com/fwlink/?LinkId=401521) para armazenar o seu estado de sessão. Também pode armazenar o estado da sessão noutros locais para garantir que a aplicação é dimensionável na nuvem. Para saber mais sobre alternativa soluções, leia [Sessão modos em estado](https://msdn.microsoft.com/library/ms178586).

## <a name="run-method-should-not-be-async"></a>Método de executar não deve ser assíncrona

### <a name="id"></a>ID

AP1000

### <a name="description"></a>Descrição

Criar assíncronos métodos (por exemplo, a [aguardar](https://msdn.microsoft.com/library/hh156528.aspx)) fora do método [(Long Directory)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) e, em seguida, ligar os métodos de assíncrona partir [(Long Directory)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Declarar o método [[também](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) como assíncronas faz com que a função de trabalho introduzir um ciclo reiniciar.

Partilhe as suas ideias e feedback na [análise de código do Azure comentários](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Chamar métodos assíncrona dentro do método [também](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) faz com que o tempo de execução do serviço na nuvem para reciclagem a função de trabalho. Quando uma função de trabalho for iniciado, todas as execuções ocorre dentro do método [(Long Directory)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . Sair do método [também](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) faz com que a função de trabalho reiniciar. Quando o tempo de execução de função trabalhador acertos no método assíncrono, distribui todas as operações após o método de assíncrona e, em seguida, devolve. Isto faz com que a função de trabalho sair do método [[[[(Long Directory)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) e reiniciar. Da próxima iteração de execução, a função de trabalho acertos no método assíncrono novamente e reinicia, a causar a função de trabalho para a Reciclagem novamente, assim.

### <a name="solution"></a>Solução

Coloque todas as operações de assíncrona fora o método [(Long Directory)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . Chame, em seguida, o método de mudarem assíncrona a partir de dentro o método [[(Long Directory)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , tal como RunAsync () .wait. A ferramenta de análise de código do Azure pode ajudar a resolver este problema.

O fragmento de código seguinte demonstra a fix de código para este problema:

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## <a name="use-service-bus-shared-access-signature-authentication"></a>Utilize a autenticação de serviço Bus partilhado acesso assinatura

### <a name="id"></a>ID

AP2000

### <a name="description"></a>Descrição

Utilize assinatura partilhadas do Access (SA) para autenticação. Serviço de controlo de acesso (ACS) está a ser preterida para autenticação de bus de serviço.

Partilhe as suas ideias e feedback na [análise de código do Azure comentários](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Para maior segurança, o Azure Active Directory é substituir autenticação ACS com autenticação SA. Consulte o artigo [Azure Active Directory é o futuro da ACS](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx) para obter informações sobre o plano de transição.

### <a name="solution"></a>Solução

Utilize a autenticação SA as suas aplicações. O exemplo seguinte mostra como utilizar um token de SA existente para aceder a um espaço de nomes do serviço bus ou entidade.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Consulte os tópicos seguintes para obter mais informações.

- Para uma descrição geral, consulte o artigo [Partilhado autenticação de assinatura do acesso com Bus de serviço](https://msdn.microsoft.com/library/dn170477.aspx)

- [Como utilizar a autenticação de assinatura de acesso partilhado com Bus de serviço](https://msdn.microsoft.com/library/dn205161.aspx)

- Para um projeto de exemplo, consulte [utilizar partilhado acesso assinatura (SA) a autenticação com subscrições do serviço Bus](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>Considere utilizar o método de OnMessage para evitar "receber ciclo"

### <a name="id"></a>ID

AP2002

### <a name="description"></a>Descrição

Para evitar a ser entregues "ciclo receber", chamar o método **OnMessage** é uma melhor solução para receber mensagens de que o método de **receção** que entra em contacto. No entanto, se tem de utilizar o método de **receção** e especificar um servidor não predefinida tempo de espera, certifique-se o tempo de espera de servidor mais do que um minuto.

Partilhe as suas ideias e feedback na [análise de código do Azure comentários](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Quando chamar **OnMessage**, o cliente inicia uma bomba interno da mensagem que consulta constantemente a fila ou subscrição. Bomba esta mensagem contém um ciclo de infinito uma chamada a receber mensagens de problemas. Se a chamada o tempo limite, problemas de uma nova chamada. Intervalo de tempo limite é determinado pelo valor da propriedade [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) da [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)que está a ser utilizado.

A vantagem de utilizar **OnMessage** em comparação comparada a **receber** é que os utilizadores não têm a manualmente as para mensagens de inquérito, processar exceções, várias mensagens em paralelo do processo e concluir as mensagens.

Se chamar **receção** sem utilizar o valor predefinido, certifique-se de que o valor de *ServerWaitTime* é mais do que um minuto. Definir *ServerWaitTime* para mais de um minuto impede que o servidor de esgotar o tempo limite antes da mensagem é recebida totalmente.

### <a name="solution"></a>Solução

Consulte os exemplos seguintes do código para utilizações recomendadas. Para obter mais detalhes, consulte o artigo [Método de QueueClient.OnMessage (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx)e [O método de QueueClient.Receive (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Para melhorar o desempenho infraestrutura do Azure mensagens, consulte o artigo o padrão de estrutura [Assíncrona introdução mensagens](https://msdn.microsoft.com/library/dn589781.aspx).

Segue-se um exemplo de utilização de **OnMessage** para receber mensagens.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

Segue-se um exemplo de utilização de **receção** com o tempo de espera de servidor predefinido.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

Segue-se um exemplo de utilização de **receção** com um período de tempo de servidor não predefinida.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## <a name="consider-using-asynchronous-service-bus-methods"></a>Considere a utilização de métodos de serviço Bus assíncronos

### <a name="id"></a>ID

AP2003

### <a name="description"></a>Descrição

Utilize assíncronos serviço Bus métodos para melhorar o desempenho com mensagens juntamente.

Partilhe as suas ideias e feedback na [análise de código do Azure comentários](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Através de métodos assíncronos permite simultaneidade do programa de aplicação, uma vez que executar cada chamada não bloquear o tópico principal. Quando utilizar o serviço Bus métodos de mensagens, a executar uma operação (enviar, receber, eliminar, etc.) demora tempo. Desta vez inclui o processamento da operação pelo serviço Bus de serviço para além da latência do pedido e a resposta. Para aumentar o número de operações por hora, operações tem de executar em simultâneo. Para obter mais informações consulte [Práticas recomendadas para o desempenho melhorias utilizar o serviço Bus controlada Messaging](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### <a name="solution"></a>Solução

Consulte o artigo [QueueClient escolares (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) para obter informações sobre como utilizar o método assíncrono recomendado.

Para melhorar o desempenho infraestrutura do Azure mensagens, consulte o artigo o padrão de estrutura [Assíncrona introdução mensagens](https://msdn.microsoft.com/library/dn589781.aspx).

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Considere a partições serviço Bus filas e tópicos

### <a name="id"></a>ID

AP2004

### <a name="description"></a>Descrição

Filas de serviço Bus partição e tópicos para um melhor desempenho com Bus de serviço de mensagens.

Partilhe as suas ideias e feedback na [análise de código do Azure comentários](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Criar a partições filas Bus de serviço e tópicos de aumentam a disponibilidade de débito e serviço de desempenho porque o débito geral de uma partição fila ou tópico já não é limitado pelo desempenho de um corretor única mensagem ou um arquivo de mensagens. Além disso, uma falha de temporária de um arquivo de mensagens não tornar uma fila com partições ou tópico indisponível. Para mais informações, consulte o artigo [Criar partições entidades de mensagens](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### <a name="solution"></a>Solução

O fragmento de código seguinte mostra como criar a partições entidades de mensagens.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Para obter mais informações, consulte o artigo [divididos serviço Bus as filas e tópicos | Blogue do Microsoft Azure](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) e dar saída a amostra do [Microsoft Azure Service Bus divididos fila de espera](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f) .

## <a name="do-not-set-sharedaccessstarttime"></a>Não fazer para definir SharedAccessStartTime

### <a name="id"></a>ID

AP3001

### <a name="description"></a>Descrição

Deve evitar utilizar SharedAccessStartTimeset para a hora atual para começar imediatamente a política de acesso partilhado. Só tem de definir esta propriedade se pretende iniciar a política de acesso partilhado tarde.

Partilhe as suas ideias e feedback na [análise de código do Azure comentários](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Sincronização do relógio faz com que uma diferença de tempo ligeira entre centros de dados. Por exemplo, é possível logicamente imaginar definir a hora de início de uma política de SA armazenamento como a hora atual utilizando DateTime. Now ou um método semelhante causará a política de SA entre em vigor imediatamente. No entanto, as diferenças ligeira tempo entre centros de dados podem causar problemas com o seguinte, uma vez que algumas vezes do Centro de dados poderão ser ligeiramente mais tarde do que a hora de início, enquanto outras pessoas antes da data-lo. Como resultado, a política de SA pode expirar rapidamente (ou mesmo imediatamente) se o tempo de vida de política estiver definido demasiado pequeno.

Para obter mais orientações sobre como utilizar partilhado assinatura de acesso no Azure armazenamento, consulte o artigo [Introdução ao tabela SA (partilhados acesso assinatura), SA de fila de espera e atualizar para Blob SA - blogue de equipa de armazenamento do Microsoft Azure - Site Home - MSDN blogues](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Solução

Remova a instrução que define a hora de início da política de acesso partilhado. A ferramenta de análise de código do Azure fornece uma correção para este problema. Para mais informações sobre a gestão de segurança, consulte o artigo o padrão de estrutura [Valet chave padrão](https://msdn.microsoft.com/library/dn568102.aspx).

O fragmento de código seguinte demonstra a correcção de código para este problema.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>Partilhada a política de acesso tempo de expiração tem de ser mais de cinco minutos

### <a name="id"></a>ID

AP3002

### <a name="description"></a>Descrição

Podem existir, tanto quanto diferença de um minuto cinco no relógio entre centros de dados em diferentes localizações devido a uma condição conhecida como "relógio distorção." Para impedir que as associações de segurança token de política de expiração mais cedo que planeado, defina a hora de expiração para ser mais de cinco minutos.

Partilhe as suas ideias e feedback na [análise de código do Azure comentários](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Centros de dados em diferentes localizações no mundo sincronizar por um sinal de relógio. Uma vez que demora tempo para sinal do relógio até localizações diferentes, podem existir um desvio de tempo entre os centros de dados em diferentes localizações geográficas apesar de tudo o que está a ser sincronizado supostamente. Esta diferença de tempo pode afetar a Shared Access política Iniciar tempo e expiração intervalo. Por conseguinte, para garantir a política de acesso partilhado entra em vigor imediatamente, a não especificar a hora de início. Além disso, certifique-se o tempo de expiração mais do que 5 minutos para impedir que o tempo limite antecipada.

Para mais informações sobre como utilizar partilhado assinatura de acesso no Azure armazenamento, consulte o artigo [Introdução ao tabela SA (partilhados acesso assinatura), SA de fila de espera e atualizar para BLOBs SA - blogue de equipa de armazenamento do Microsoft Azure - Site Home - MSDN blogues](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Solução

Para obter mais informações sobre a gestão de segurança, consulte o padrão de estrutura [Valet chave padrão](https://msdn.microsoft.com/library/dn568102.aspx).

Segue-se um exemplo de não especificando uma hora de início de política de acesso partilhado.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Segue-se um exemplo de especificar uma hora de início de política de acesso partilhado com uma duração de expiração de política maior mais de cinco minutos.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Para mais informações, consulte o artigo [criar e utilizar uma assinatura de acesso partilhado](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="use-cloudconfigurationmanager"></a>Utilizar CloudConfigurationManager

### <a name="id"></a>ID

AP4000

### <a name="description"></a>Descrição

Utilizando a classe de [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) para projetos como o Web site do Azure e serviços móveis Azure não apresentar runtime problemas. Como prática recomendada, no entanto, é aconselhável utilizar nuvem[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) como uma forma unificada de gestão de configurações para todas as aplicações do Azure nuvem.

Partilhe as suas ideias e feedback na [análise de código do Azure comentários](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

CloudConfigurationManager lê o ficheiro de configuração adequado para o ambiente de aplicação.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>Solução

Reestruturar o seu código para utilizar a [Classe de CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). Uma solução de código para este problema é fornecida pela ferramenta de análise de código do Azure.

O fragmento de código seguinte demonstra a fix de código para este problema. Substituir

`var settings = ConfigurationManager.AppSettings["mySettings"];`

com

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Eis um exemplo de como armazenar a definição de configuração num ficheiro App ou Web. config. Adicione as definições para a secção appSettings do ficheiro de configuração. Segue-se o ficheiro da Web. config para o exemplo anterior do código.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## <a name="avoid-using-hard-coded-connection-strings"></a>Evitar utilizar cadeias de ligação codificada

### <a name="id"></a>ID

AP4001

### <a name="description"></a>Descrição

Se utilizar cadeias de ligação codificada e necessita de atualizá-las mais tarde, terá de efetuar alterações ao seu código de origem e recompilar a aplicação. No entanto, se armazenar cadeias de ligação num ficheiro de configuração, pode alterá-las mais tarde simplesmente atualizando o ficheiro de configuração.

Partilhe as suas ideias e feedback na [análise de código do Azure comentários](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Disco rígido codificação cadeias de ligação é uma prática incorretas, porque-apresenta problemas quando as cadeias de ligação têm de ser alterados rapidamente. Além disso, se o projeto tem de ser verificado ao controlo de origem, as cadeias de ligação codificada apresentam vulnerabilidade de segurança uma vez que as cadeias podem ser visualizadas no código fonte.

### <a name="solution"></a>Solução

Armazenar cadeias de ligação em ficheiros de configuração ou ambientes Azure.

- Para as aplicações de autónomo, utilize App para armazenar as definições de cadeia de ligação.

- Para as aplicações web alojado no IIS, utilize a Web. config para armazenar as cadeias de ligação.

- Para aplicações do ASP.NET vNext, utilize configuration.json para armazenar as cadeias de ligação.

Para obter informações sobre como utilizar ficheiros de configurações como Web. config ou App, consulte [Diretrizes de configuração de Web do ASP.NET](https://msdn.microsoft.com/library/vstudio/ff400235(v=vs.100).aspx). Para obter informações sobre como Azure ambiente variáveis trabalho, consulte o artigo [Azure Web Sites: como cadeias da aplicação e ligação cadeias funcionar](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Para obter informações sobre como armazenar cadeia de ligação no controlo de fonte, consulte o artigo [Evite colocar informações confidenciais como cadeias de ligação nos ficheiros que estão armazenados num repositório de código de origem](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## <a name="use-diagnostics-configuration-file"></a>Utilizar o ficheiro de configuração de diagnóstico

### <a name="id"></a>ID

AP5000

### <a name="description"></a>Descrição

Em vez de configurar as definições de diagnósticos no seu código, como utilizando Microsoft.WindowsAzure.Diagnostics API de programação, deve configurar definições dos diagnósticos no ficheiro diagnostics.wadcfg. (Ou, diagnostics.wadcfgx se utiliza o Azure SDK 2,5). Ao fazê-lo, pode alterar definições dos diagnósticos sem ter de voltar a compilar o código.

Partilhe as suas ideias e feedback na [análise de código do Azure comentários](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Antes de Azure SDK 2,5 (que utiliza diagnósticos do Azure 1.3), Azure diagnósticos (WAD) pôde ser configurada utilizando vários métodos diferentes: adicioná-lo para o blob de configuração no armazenamento, utilizando o código obrigatório, a configuração declarativa ou a configuração predefinida. No entanto, a forma preferida configurar diagnósticos é utilizar um ficheiro de configuração de XML (diagnostics.wadcfg ou diagnositcs.wadcfgx para SDK 2,5 e posterior) do projeto de aplicação. Nesta abordagem, o ficheiro diagnostics.wadcfg completamente define a configuração e pode ser atualizado e novamente implementado no irá. Misturar a utilização do ficheiro de configuração diagnostics.wadcfg com os métodos de configurações de definição de programação, utilizando as classes [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)ou [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)pode conduzir a ambiguidade. Para mais informações, consulte [a inicialização ou alterar Azure diagnósticos de configuração](https://msdn.microsoft.com/library/azure/hh411537.aspx) .

Começando por 1.3 WAD (incluído no Azure SDK 2,5), já não é possível utilizar o código para configurar diagnósticos. Como resultado, apenas pode fornecer a configuração ao aplicar ou atualizar a extensão de diagnóstico.

### <a name="solution"></a>Solução

Utilize o designer de configuração de diagnóstico para mover as definições de diagnóstico para o ficheiro de configuração de diagnóstico (diagnositcs.wadcfg ou diagnositcs.wadcfgx para SDK 2,5 e posterior). Também é recomendado que instalar o [Azure SDK 2.5](http://go.microsoft.com/fwlink/?LinkId=513188) e utilize a funcionalidade diagnósticos mais recente.

1. No menu de atalho para a função que pretende configurar, selecione propriedades e, em seguida, selecione o separador de configuração.

1. Na secção **Diagnósticos** , certifique-se de que a caixa de verificação **Ativar diagnósticos** está selecionada.

1. Selecione o botão de **Configurar** .

  ![Aceder a opção ativar diagnósticos](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

  Para mais informações, consulte [Configurar diagnósticos para serviços em nuvem Azure e máquinas virtuais](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) .


## <a name="avoid-declaring-dbcontext-objects-as-static"></a>Evitar declarar DbContext objetos como estático

### <a name="id"></a>ID

AP6000

### <a name="description"></a>Descrição

Para guardar memória, evite declarar DBContext objetos como estático.

Partilhe as suas ideias e feedback na [análise de código do Azure comentários](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Objetos de DBContext mantenha os resultados da consulta a partir de cada chamada. Em objetos estáticos DBContext não são eliminados até que o domínio da aplicação é descarregado. Por conseguinte, um objecto DBContext estático pode consumir grandes quantidades de memória.

### <a name="solution"></a>Solução

Declarar DBContext como uma variável local ou campo não estático instância, utilizá-la para uma tarefa e, em seguida, reproduza-a ser eliminados após a sua utilização.

O exemplo seguinte classe de controlador MVC mostra como usar o objeto DBContext.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre optimzing e Azure apps de resolução de problemas, consulte o artigo [resolver problemas de uma aplicação web na aplicação de serviço de Azure utilizando o Visual Studio](./app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).
