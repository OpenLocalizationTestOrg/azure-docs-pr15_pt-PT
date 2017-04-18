<properties 
    pageTitle="As transações de serviço Bus | Microsoft Azure" 
    description="Descrição geral das transações atómica Bus de serviço do Azure e enviar por" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="10/04/2016"
    ms.author="clemensv;sethm"/>

# <a name="overview-of-service-bus-transaction-processing"></a>Descrição geral do processamento de transações Bus de serviço

Este artigo aborda as capacidades da transação do Azure Service Bus. Muito do debate é ilustrado pelas [Transações atómica com Bus de serviço de amostra](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions). Este artigo é limitado para uma descrição geral do processamento de transações e a funcionalidade *Enviar por* no serviço Bus, enquanto a amostra de transações atómica é mais abrangente sobre e mais complexas no âmbito.

## <a name="transactions-in-service-bus"></a>Transações na Bus de serviço

Uma [transação](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions#what-are-transactions) grupos duas ou mais operações em conjunto para um *âmbito de execução*. Pela natureza, uma transação da tem de garantir que todas as operações de um determinado grupo de operações teve êxito ou em parceria falharem. Este propósito transações agir como uma unidade é, muitas vezes, *atomicidade*. 

Serviço Bus é um corretor de mensagem transaccional e garante a integridade transaccional para todas as operações internas contra a suas arquivos de mensagens. Todas as transferências de mensagens dentro de serviço Bus, tal como se mover mensagens para uma [fila entregues](service-bus-dead-letter-queues.md) ou [reencaminhamento automático](service-bus-auto-forwarding.md) de mensagens entre entidades, são transaccionais. Como tal, se o serviço Bus aceita uma mensagem,-lo já foi armazenado e identificado com um número de sequência. De, em seguida, em qualquer transferências, mensagem dentro de serviço Bus serem operações coordenadas entidades e irá nenhuma conduzir a perda (é concluída com êxito de origem e destino falha) ou para duplicação (falha de origem e destino é concluída com êxito) da mensagem.

Serviço Bus suporta operações de agrupamento contra uma única mensagens entidade (fila de espera, tópico, subscrição) no âmbito de uma transação. Por exemplo, pode enviar várias mensagens para uma fila a partir de um âmbito da transação e as mensagens só serão consolidadas registo na fila quando a transação é concluída com êxito.

## <a name="operations-within-a-transaction-scope"></a>Operações dentro de um âmbito da transação 

As operações que podem ser executadas dentro de um âmbito da transação são os seguintes:

- ** [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx), [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx), [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)**: enviar, SendAsync, SendBatch, SendBatchAsync 

- **[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)**: concluída, CompleteAsync, Abandon, AbandonAsync, mensagens não entregues, DeadletterAsync, diferir, DeferAsync, RenewLock, RenewLockAsync 

Receber operações não são incluídas, uma vez que é considerada que a aplicação adquire mensagens utilizando o modo de [ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , dentro de alguns recebem ciclo ou com uma [OnMessage](https://msdn.microsoft.com/library/azure/dn369601.aspx) chamada de retorno e, em seguida, só é aberto um âmbito da transação para processar a mensagem.

A eliminação da mensagem (concluída, abandon, entregues, diferir), em seguida, ocorre o âmbito do e dependentes no, o resultado da transação geral.

## <a name="transfers-and-send-via"></a>Transferências e "enviar por"

Para ativar a entrega transaccional dos dados a partir de uma fila para um processador e, em seguida, para outra fila, o serviço Bus suporta *transferências*. Numa operação de transferência, um remetente pela primeira vez envia uma mensagem para uma fila"transferir" e a fila de transferência move imediatamente a mensagem para a fila de ao destino pretendido utilizando a mesma transferência robusta implementação do que a funcionalidade de reencaminhar automática baseia-se em. A mensagem nunca está empenhada em registo na fila de transferência de uma forma que torna-se visíveis para os consumidores de na fila de transferência.

O poder desta capacidade transaccional fica evidente quando a própria fila de transferência é a origem de mensagens de entrada do remetente. Por outras palavras, serviço Bus pode transferir a mensagem para a fila de destino "por" fila de transferência, enquanto executa uma completa (ou diferir, ou entregues) operação na mensagem de entrada, todos os numa operação atómica. 

### <a name="see-it-in-code"></a>Vê-la no código

Para configurar essas transferências, crie um remetente da mensagem que destina-se a fila de destino através da fila de transferência. Também terá um destinatário que obtém mensagens de nessa mesma fila. Por exemplo:

```
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

Em seguida, uma transação simple utiliza estes elementos, tal como no exemplo seguinte:

```
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>Próximos passos

Consulte os artigos seguintes para obter mais informações sobre filas Bus de serviço:

- [Entidades de serviço Bus em cadeia com o reencaminhamento de automática](service-bus-auto-forwarding.md)
- [Exemplo de reencaminhar automática](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AutoForward)
- [Transações atómica com exemplo Bus de serviço](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions)
- [Azure filas e serviço Bus filas comparado comparadas](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Como utilizar filas Bus de serviço](service-bus-dotnet-get-started-with-queues.md)