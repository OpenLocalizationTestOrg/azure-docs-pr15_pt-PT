<properties
    pageTitle="Como utilizar tópicos serviço Bus com Java | Microsoft Azure"
    description="Saiba como utilizar a subscrições e tópicos de serviço Bus no Azure. Exemplos de código são escritos para aplicações Java."
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Como utilizar a subscrições e tópicos de Bus de serviço

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este guia descreve como utilizar a subscrições e tópicos de Bus de serviço. Os exemplos são gravados no Java e utilizam o [Azure SDK para Java][]. Os cenários abrangidos incluem **criar tópicos e subscrições**, **criar filtros de subscrição**, **enviar mensagens a um tópico**, **receber mensagens de uma subscrição**e **Eliminar tópicos e subscrições**.

## <a name="what-are-service-bus-topics-and-subscriptions"></a>O que são tópicos Bus de serviço e subscrições?

Tópicos de serviço Bus subscrições suportam e um *Publicar/subscreva o* modelo de comunicação de mensagens. Quando utilizar tópicos e subscrições, componentes de uma aplicação distribuída não comunicarem diretamente com outro; em vez disso, eles trocarem mensagens através de um tópico que funciona como um intermédio.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Ao contrário filas Bus de serviço, em que cada mensagem é processada por um único consumidor, tópicos e subscrições fornecem uma forma de "um-para-muitos" de comunicação, utilizando um padrão de publicar/subscrever. É possível registar múltiplas subscrições a um tópico. Quando uma mensagem é enviada para um tópico, em seguida, é tornado disponíveis para cada subscrição alça/processo de forma independente.

Uma subscrição de um tópico é semelhante a uma fila de espera virtual que recebe cópias das mensagens que foram enviadas para o tópico. Opcionalmente, pode registar regras de filtro para um tópico por subscrição base em, que permite-lhe filtro/restringir um tópico que mensagens são recebidas por quais as subscrições tópico.

Tópicos de serviço Bus e subscrições permitem-lhe dimensionar para processar um grande número de mensagens através de um grande número de utilizadores e de aplicações.

## <a name="create-a-service-namespace"></a>Criar um espaço de nomes de serviço

Para começar a utilizar a subscrições e tópicos de serviço Bus no Azure, primeiro tem de criar um espaço de nomes de serviço. Um espaço de nomes fornece um âmbito contentor para endereçar recursos de serviço Bus na aplicação.

Para criar um espaço de nomes:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a aplicação para utilizar o serviço Bus

Certifique-se de que instalou o [Azure SDK para Java][] antes de criar este exemplo. Se estiver a utilizar Eclipse, pode instalar o [Toolkit de Azure para Eclipse][] que inclui o SDK do Azure para Java. Em seguida, pode adicionar as **Bibliotecas do Microsoft Azure para Java** ao seu projeto:

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Adicione as seguintes instruções de importar para a parte superior do ficheiro Java:

```
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

Adicione as bibliotecas do Azure para Java para o seu caminho de compilação e inclui-los no seu assemblagem de implementação do projeto.

## <a name="create-a-topic"></a>Criar um tópico

Operações de gestão para tópicos do serviço Bus podem ser efetuadas pela classe de **ServiceBusContract** . Um objeto de **ServiceBusContract** fórmula foi criado com uma configuração adequada que contém o token de SA com permissões para geri-lo e, a classe de **ServiceBusContract** é o único ponto de comunicação com Azure.

A classe de **ServiceBusService** fornece métodos para criar, enumerar e eliminar tópicos. O exemplo seguinte mostra como um objeto de **ServiceBusService** pode ser utilizado para criar um tópico denominado `TestTopic`, com um espaço de nomes denominado `HowToSample`:

    Configuration config =
        ServiceBusConfiguration.configureWithSASAuthentication(
          "HowToSample",
          "RootManageSharedAccessKey",
          "SAS_key_value",
          ".servicebus.windows.net"
          );

    ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
    try  
    {
        CreateTopicResult result = service.createTopic(topicInfo);
    }
    catch (ServiceException e) {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Existem métodos no **TopicInfo** que permitem propriedades do tópico para ser definidas (por exemplo: para predefinir time to live (TTL) um valor para serem aplicados a mensagens enviadas para o tópico). O exemplo seguinte mostra como criar um tópico denominado `TestTopic` com um tamanho máximo de 5 GB:

    long maxSizeInMegabytes = 5120;  
    TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateTopicResult result = service.createTopic(topicInfo);

Tenha em atenção que pode utilizar o método de **listTopics** no **ServiceBusContract** objetos para verificar se já existe um tópico com um nome especificado dentro de um espaço de nomes de serviço.

## <a name="create-subscriptions"></a>Criar subscrições

Subscrições para tópicos também são criadas com a classe de **ServiceBusService** . Subscrições são com nome e podem ter um filtro opcional que restringe o conjunto de mensagens passado para fila de espera virtual da subscrição.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro predefinido (MatchAll)

O filtro de **MatchAll** é o filtro predefinido que é utilizado se não for especificado nenhum filtro quando é criada uma nova subscrição. Quando é utilizado o filtro de **MatchAll** , todas as mensagens publicadas para o tópico são colocadas na fila de espera virtual da subscrição. O exemplo seguinte cria uma subscrição denominada "AllMessages" e utiliza o filtro de **MatchAll** predefinido.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result =
        service.createSubscription("TestTopic", subInfo);

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com os filtros

Também pode criar filtros que lhe permitem ao âmbito do que as mensagens enviadas para um tópico devem ser apresentada dentro de uma subscrição de tópico específico.

O tipo de filtro suportado em subscrições mais flexível é o [SqlFilter][], que implementa um subconjunto de SQL92. Trabalhar com os filtros SQL nas propriedades das mensagens que são publicadas para o tópico. Para obter mais detalhes sobre as expressões que podem ser utilizadas com um filtro SQL, reveja a sintaxe de [SqlFilter.SqlExpression][] .

O exemplo seguinte cria uma subscrição denominada `HighMessages` com um objeto de [SqlFilter][] apenas seleciona todas as mensagens com uma propriedade personalizada de **MessageNumber** maior do que 3:

```
// Create a "HighMessages" filtered subscription  
SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "HighMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "HighMessages", "$Default");
```

Do mesmo modo, o exemplo seguinte cria uma subscrição denominada `LowMessages` com um objeto de [SqlFilter][] apenas seleciona todas as mensagens com uma **MessageNumber** propriedade menor ou igual a 3:

```
// Create a "LowMessages" filtered subscription
SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "LowMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "LowMessages", "$Default");
```

Quando uma mensagem agora é enviada para `TestTopic`, sempre será enviado para destinatários subscritos o `AllMessages` subscrição e seletivamente entregue para destinatários subscritos o `HighMessages` e `LowMessages` subscrições (consoante o conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

Para enviar uma mensagem a um tópico de serviço Bus, a aplicação obtém um objeto de **ServiceBusContract** . O código seguinte demonstra como enviar uma mensagem a `TestTopic` criado anteriormente dentro do tópico de `HowToSample` espaço de nomes:

```
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

As mensagens enviadas para o serviço Bus tópicos são instâncias da classe [BrokeredMessage][] . [BrokeredMessage][] *os objetos têm um conjunto de métodos padrão (tais como * *setLabel* * e * *TimeToLive**), um dicionário que é utilizado para colocar em espera propriedades personalizadas de específicos da aplicação bem como um corpo dos dados da aplicação arbitrário. Uma aplicação pode definir o corpo da mensagem, passando qualquer objeto serializável para o construtor do [BrokeredMessage][]e o adequado * *DataContractSerializer* * , em seguida, será utilizada para serializar o objeto. Em alternativa, um * *java.io.InputStream** pode ser fornecido.

O exemplo seguinte demonstra como enviar mensagens para de cinco teste a `TestTopic` **MessageSender** podemos obtidos no fragmento de código acima.
Tenha em atenção como o valor da propriedade **MessageNumber** de cada mensagem varia em iteração do ciclo (Isto irá determinar quais as subscrições recebem-la):

```
for (int i=0; i<5; i++)  {
// Create message, passing a string message for the body
BrokeredMessage message = new BrokeredMessage("Test message " + i);
// Set some additional custom app-specific property
message.setProperty("MessageNumber", i);
// Send message to the topic
service.sendTopicMessage("TestTopic", message);
}
```

Tópicos de serviço Bus suportam um tamanho máximo da mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe nenhum limite o número de mensagens contidos num tópico mas há um remate no tamanho total das mensagens contidos por um tópico. Tamanho este tópico é definido no momento de criação, com um limite máximo de 5 GB.

## <a name="how-to-receive-messages-from-a-subscription"></a>Como receber mensagens de uma subscrição

Para receber mensagens de uma subscrição, utilize um objecto **ServiceBusContract** . Mensagens recebidas podem trabalhar em dois modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Quando utilizar o modo **ReceiveAndDelete** , receber é uma operação de captura único - isto é, quando o serviço Bus recebe um pedido de leitura para uma mensagem,-marca a mensagem como sendo consumidas e devolve-o para a aplicação. Modo de **ReceiveAndDelete** é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-la. Porque Bus serviço será tiver marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa consumir mensagens novamente,-lo será não ter visto a mensagem que foi consumida antes da falha.

No modo de **PeekLock** , receber torna-se uma operação de duas fases, que torna possível para suportar aplicações que não poder permitir mensagens em falta. Quando o serviço Bus recebe um pedido, localiza a próxima mensagem para ser consumida, bloqueios-la para impedir que outros consumidores recebê-la e, em seguida, devolve-o para a aplicação. Depois da aplicação de concluir o processamento a mensagem (ou armazena sujeito para processamento futuro), conclui a segunda fase do processo de receção ao contactar o suporte **Eliminar** a mensagem recebida. Quando o serviço Bus vê a chamada **Eliminar** , irá marcar a mensagem como sendo consumidas e removê-lo do tópico.

O exemplo abaixo demonstra como mensagens podem ser recebidas e processadas a utilizar o modo de **PeekLock** (não o modo de predefinido). O exemplo abaixo executa um ciclo e processa mensagens na subscrição "HighMessages" e, em seguida, sai quando não existem mais mensagens (em alternativa,-pode ser definido de esperar que as novas mensagens).

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
        ReceiveSubscriptionMessageResult  resultSubMsg =
            service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
        BrokeredMessage message = resultSubMsg.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the topic message.
            System.out.print("From topic: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MessageNumber"));
            // Delete message.
            System.out.println("Deleting this message.");
            service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como lidar falhas de aplicações e legível mensagens

Serviço Bus fornece funcionalidade para ajudá-lo a recuperar correctamente de erros na sua aplicação ou dificuldades processamento de uma mensagem. Se uma aplicação recetor for não é possível processar a mensagem por algum motivo,-pode ligar o método de **unlockMessage** mensagem recebida (em vez do método **deleteMessage** ). Isto vai originar Bus de serviço para desbloquear a mensagem no tópico de e torná-lo disponível para ser recebidos novamente, pela mesma aplicação consome ou por outra aplicação consome.

Também existe um limite de tempo associado a uma mensagem bloqueada dentro do tópico e se a aplicação falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a aplicação provoca uma falha), em seguida, o serviço Bus serão desbloquear automaticamente a mensagem e torná-lo disponível para ser recebidos novamente.

Se que a aplicação falha após a transformação a mensagem, mas antes do pedido de **deleteMessage** é emitido, em seguida, a mensagem vai ser reenviada para a aplicação quando é reiniciado. Esta opção é geralmente denominada **, Pelo menos, uma vez processamento**, ou seja, cada mensagem será processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser sido novamente entregues. Se o cenário de não poder permitir o processamento de duplicados, em seguida, os programadores de aplicações devem adicionar lógica adicional para a sua aplicação para processar entrega de mensagens duplicados. Muitas vezes fazê-utilizando o método de **getMessageId** da mensagem, será que permaneçam constante ao longo de tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições

A forma principal para eliminar tópicos e subscrições é utilizar um objeto de **ServiceBusContract** . Eliminar um tópico também irá eliminar quaisquer subscrições que estão registadas com o tópico. Subscrições também podem ser eliminadas forma independente.

```
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos filas Bus de serviço, consulte o artigo [filas Bus de serviço, tópicos e subscrições][] para obter mais informações.

  [Azure SDK para Java]: http://azure.microsoft.com/develop/java/
  [Toolkit Azure para Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Azure classic portal]: http://manage.windowsazure.com/
  [Serviço Bus filas, tópicos e subscrições]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx 
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  
  [0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
  [2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
  [3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
