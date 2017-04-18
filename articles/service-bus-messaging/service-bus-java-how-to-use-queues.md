<properties
    pageTitle="Como utilizar filas de serviço Bus com Java | Microsoft Azure"
    description="Saiba como utilizar filas de serviço Bus no Azure. Exemplos de código escritos Java."
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Como utilizar filas Bus de serviço

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artigo descreve como utilizar filas Bus de serviço. Os exemplos são gravados no Java e utilizam o [Azure SDK para Java][]. Os cenários abrangidos incluem **criar filas**, **Enviar e receber mensagens**e **Eliminar filas**.

## <a name="what-are-service-bus-queues"></a>O que são filas de Bus de serviço?

Serviço Bus filas suportam a um modelo de comunicação **juntamente mensagens** . Ao utilizar filas, componentes de uma aplicação distribuída não comunicarem diretamente com outro; em vez disso, eles trocarem mensagens através de uma fila, que age como um intermédio (corretor). Um produtor de mensagem (remetente) mãos desativar uma mensagem para a fila e, em seguida, continua a sua processamento.
Forma assíncrona, um consumidor mensagem (recetor) obtém a mensagem da fila de espera e processa-lo. Produtor não tem de aguardar uma resposta do consumidor para poder continuar a processar e ainda mais enviar mensagens. Filas oferecem a entrega de mensagens **do primeiro, primeiro saída (FIFO)** para um ou mais consumidores concorrentes. Isto é, as mensagens são recebidas normalmente e processadas pelos destinatários pela ordem em que foram adicionadas à fila de espera e cada mensagem é recebida e processada pelos consumidores de apenas uma mensagem.

![QueueConcepts](./media/service-bus-java-how-to-use-queues/sb-queues-08.png)

Serviço Bus filas são uma tecnologia uso geral que pode ser utilizada para uma grande variedade de cenários:

- Comunicação entre funções web e trabalhador de uma camada com várias aplicação Azure.
- Comunicação entre aplicações no local e o Azure alojado aplicações numa solução híbrido.
- Comunicação entre os componentes de uma aplicação distribuída em execução no local em organizações diferentes ou os departamentos de uma organização.

Utilizar filas permite-lhe dimensionar mais facilmente as suas aplicações e ative RDP na sua arquitetura.

## <a name="create-a-service-namespace"></a>Criar um espaço de nomes de serviço

Para começar a utilizar filas Bus de serviço no Azure, primeiro tem de criar um espaço de nomes. Um espaço de nomes fornece um âmbito contentor para endereçar recursos de serviço Bus na aplicação.

Para criar um espaço de nomes:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a aplicação para utilizar o serviço Bus

Certifique-se de que instalou o [Azure SDK para Java][] antes de criar este exemplo. Se estiver a utilizar Eclipse, pode instalar o [Toolkit de Azure para Eclipse][] que inclui o SDK do Azure para Java. Em seguida, pode adicionar as **Bibliotecas do Microsoft Azure para Java** ao seu projeto:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Adicione o seguinte `import` declarações para a parte superior do ficheiro Java:

```
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## <a name="create-a-queue"></a>Criar uma fila

Operações de gestão para filas de serviço Bus podem ser efetuadas pela classe de **ServiceBusContract** . Um objeto de **ServiceBusContract** fórmula foi criado com uma configuração adequada que contém o token de SA com permissões para geri-lo e, a classe de **ServiceBusContract** é o único ponto de comunicação com Azure.

A classe de **ServiceBusService** fornece métodos para criar, enumerar e eliminar filas. O exemplo abaixo mostra como um objeto de **ServiceBusService** pode ser utilizado para criar uma fila denominada "TestQueue", com um espaço de nomes com o nome "HowToSample":

```
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
            "HowToSample",
            "RootManageSharedAccessKey",
            "SAS_key_value",
            ".servicebus.windows.net"
            );

ServiceBusContract service = ServiceBusService.create(config);
QueueInfo queueInfo = new QueueInfo("TestQueue");
try
{
    CreateQueueResult result = service.createQueue(queueInfo);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Existem métodos no **QueueInfo** que permitem propriedades de fila de espera para ser optimizado (por exemplo: para predefinir time to live (TTL) um valor para ser aplicadas a mensagens enviadas para a fila). O exemplo seguinte mostra como criar uma fila denominada `TestQueue` com um tamanho máximo de 5GB:

````
long maxSizeInMegabytes = 5120;
QueueInfo queueInfo = new QueueInfo("TestQueue");
queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateQueueResult result = service.createQueue(queueInfo);
````

Tenha em atenção que pode utilizar o método de **listQueues** no **ServiceBusContract** objetos para verificar se já existe uma fila com um nome especificado dentro de um espaço de nomes de serviço.

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila

Para enviar uma mensagem para uma fila Bus de serviço, a aplicação obtém um objeto de **ServiceBusContract** . O código seguinte mostra como enviar uma mensagem a `TestQueue` fila criada anteriormente na `HowToSample` espaço de nomes:

```
try
{
    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendQueueMessage("TestQueue", message);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

As mensagens enviadas para e recebeu do serviço Bus filas são instâncias da classe [BrokeredMessage][] . Objectos de [BrokeredMessage][] têm um conjunto de propriedades padrão (como [etiqueta](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), um dicionário que é utilizado para colocar em espera propriedades específicas da aplicação personalizada bem como um corpo dos dados da aplicação arbitrário. Uma aplicação pode definir o corpo da mensagem, passando qualquer objeto serializável para o construtor do [BrokeredMessage][]e o serializador adequado, em seguida, será utilizado para serializar o objeto. Em alternativa, pode fornecer um java **. ES. InputStream** objeto.

O exemplo seguinte demonstra como enviar mensagens para de cinco teste a `TestQueue` **MessageSender** podemos obtidos no fragmento de código anterior:

```
for (int i=0; i<5; i++)
{
     // Create message, passing a string message for the body.
     BrokeredMessage message = new BrokeredMessage("Test message " + i);
     // Set an additional app-specific property.
     message.setProperty("MyProperty", i);
     // Send message to the queue
     service.sendQueueMessage("TestQueue", message);
}
```

Serviço Bus filas suportam a um tamanho máximo da mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe nenhum limite o número de mensagens contidos numa fila mas há um remate no tamanho total das mensagens contidos por uma fila. Este tamanho da fila é definido a hora de criação, com um limite máximo de 5 GB.

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

A forma principal de receber mensagens de uma fila é utilizar um objeto de **ServiceBusContract** . Mensagens recebidas podem trabalhar em dois modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Quando utilizar o modo **ReceiveAndDelete** , receber é uma operação de captura único - isto é, quando o serviço Bus recebe um pedido de leitura para uma mensagem numa fila,-marca a mensagem como sendo consumidas e devolve-o para a aplicação. Modo de **ReceiveAndDelete** (que é o modo predefinido) é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-la.
Porque Bus serviço será tiver marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa consumir mensagens novamente,-lo será não ter visto a mensagem que foi consumida antes da falha.

No modo de **PeekLock** , receber torna-se uma operação de duas fases, que torna possível para suportar aplicações que não poder permitir mensagens em falta. Quando o serviço Bus recebe um pedido, localiza a próxima mensagem para ser consumida, bloqueios-la para impedir que outros consumidores recebê-la e, em seguida, devolve-o para a aplicação. Depois da aplicação de concluir o processamento a mensagem (ou armazena sujeito para processamento futuro), conclui a segunda fase do processo de receção ao contactar o suporte **Eliminar** a mensagem recebida. Quando o serviço Bus vê a chamada **Eliminar** , irá marcar a mensagem como sendo consumidas e removê-la da fila de espera.

O exemplo seguinte demonstra como mensagens podem ser recebidas e processadas a utilizar o modo de **PeekLock** (não o modo de predefinido). Exemplo que se segue é um ciclo infinito e processa as mensagens que chegam para o nosso "TestQueue":

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
         ReceiveQueueMessageResult resultQM =
                service.receiveQueueMessage("TestQueue", opts);
        BrokeredMessage message = resultQM.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the queue message.
            System.out.print("From queue: ");
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
                message.getProperty("MyProperty"));
            // Remove message from queue.
            System.out.println("Deleting this message.");
            //service.deleteMessage(message);
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

Serviço Bus fornece funcionalidade para ajudá-lo a recuperar correctamente de erros na sua aplicação ou dificuldades processamento de uma mensagem. Se uma aplicação recetor for não é possível processar a mensagem por algum motivo,-pode ligar o método de **unlockMessage** mensagem recebida (em vez do método **deleteMessage** ). Isto vai originar Bus de serviço para desbloquear a mensagem na fila de espera e torná-lo disponível para ser recebidos novamente, pela mesma aplicação consome ou por outra aplicação consome.

Também existe um limite de tempo associado a uma mensagem bloqueada dentro de fila de espera e, se a aplicação falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a aplicação falha), em seguida, o serviço Bus serão desbloquear automaticamente a mensagem e torná-lo disponível para ser recebidos novamente.

Se que a aplicação falha após a transformação a mensagem, mas antes do pedido de **deleteMessage** é emitido, em seguida, a mensagem vai ser reenviada para a aplicação quando é reiniciado. Esta opção é geralmente denominada **, Pelo menos, uma vez processamento**, ou seja, cada mensagem será processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser sido novamente entregues. Se o cenário de não poder permitir o processamento de duplicados, em seguida, os programadores de aplicações devem adicionar lógica adicional para a sua aplicação para processar entrega de mensagens duplicados. Muitas vezes fazê-utilizando o método de **getMessageId** da mensagem, será que permaneçam constante ao longo de tentativas de entrega.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos filas Bus de serviço, consulte o artigo [filas, tópicos e subscrições][] para obter mais informações.

Para mais informações, consulte o [Centro de programadores do Java](/develop/java/).


  [Azure SDK para Java]: http://azure.microsoft.com/develop/java/
  [Toolkit Azure para Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Filas, tópicos e subscrições]: service-bus-queues-topics-subscriptions.md
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

