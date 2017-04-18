<properties 
    pageTitle="Reencaminhamento de automática Bus de serviço de mensagens entidades | Microsoft Azure"
    description="Como corrente de uma subscrição outra fila ou tópico ou fila de espera."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Entidades de serviço Bus em cadeia com o reencaminhamento de automática

A funcionalidade de *reencaminhamento de chamadas automática* permite-lhe corrente de uma subscrição outra fila ou tópico que faz parte do mesmo espaço de nomes ou fila de espera. Quando reencaminhamento automático está ativado, o serviço Bus é automaticamente remove as mensagens que são colocadas na primeira fila ou subscrição (origem) e coloca-los na segunda fila ou tópico (destino). Tenha em atenção que é possível continuar enviar uma mensagem para a entidade de destino diretamente. Além disso, não é possível ao corrente de bicicleta uma subfila, tal como uma fila de mensagens não entregues, a outra fila ou tópico.

## <a name="using-auto-forwarding"></a>Utilizando o reencaminhamento de automática

Pode ativar o reencaminhamento de chamadas automática ao definir as propriedades de [SubscriptionDescription.ForwardTo][] ou [QueueDescription.ForwardTo][] os objetos [QueueDescription][] ou [SubscriptionDescription][] para a origem, tal como no exemplo seguinte.

```
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

A entidade de destino tem de existir ao tempo que é criada a entidade de origem. Se a entidade de destino não existir, o serviço Bus devolve uma exceção quando lhe for pedido para criar a entidade de origem.

Pode utilizar o reencaminhamento de chamadas automática para dimensionar um tópico individuais. Serviço Bus limita o [número de subscrições sobre um determinado tópico](service-bus-quotas.md) para 2.000. Pode acomodar subscrições adicionais através da criação de tópicos de segundo nível. Tenha em atenção que mesmo não está sujeitos a limitação de serviço Bus no número de subscrições, adicionar um segundo nível de tópicos pode melhorar o débito geral do seu tópico.

![Cenário de reencaminhamento de chamadas automática][0]

Também pode utilizar o reencaminhamento de chamadas automática para separar os remetentes de mensagem de destinatários. Por exemplo, considere um sistema ERP é constituído por três módulos: Processamento Encomendas, gestão de inventário e gestão de relações de cliente. Cada um destes módulos gera mensagens que são colocado em fila para um tópico correspondente. Alice e o Rui sejam representantes de vendas que estão interessados em todas as mensagens que se relacionam com aos seus clientes. Para receber as mensagens, o Alice e João criar uma fila pessoal e uma subscrição em cada um dos tópicos ERP que reencaminhar automaticamente todas as mensagens para os respetivos fila de espera.

![Cenário de reencaminhamento de chamadas automática][1]

Se vai Alice no férias, respetivos fila pessoal, em vez do tópico ERP, preenche para cima. Neste cenário, uma vez que um representante de vendas não recebeu todas as mensagens, nenhum dos tópicos ERP nunca atingir quota.

## <a name="auto-forwarding-considerations"></a>Considerações sobre o reencaminhamento de chamadas automática

Se a entidade de destino acumulado muitas mensagens e excede a quota, ou a entidade de destino estiver desactivada, a entidade de origem adiciona as mensagens à sua [entregues fila de espera](service-bus-dead-letter-queues.md) até que existe espaço no destino (ou a entidade é activada novamente). Essas mensagens irão continuar a encontram-se na fila de espera entregues, pelo que deve receber e processá-los a partir da fila entregues explicitamente.

Quando a interligação de em conjunto tópicos individuais para obter um tópico composto com muitas subscrições, é recomendável que tem um número moderado de subscrições no tópico de primeiro nível e muitos subscrições nos tópicos de segundo nível. Por exemplo, um tópico de primeiro nível com 20 subscrições, cada um deles ligados a um tópico de segundo nível com 200 subscrições, em permite débito superior de um tópico de primeiro nível com 200 subscrições, cada ligados na um tópico de segundo nível, com 20 subscrições.

Serviço Bus contas a pagar uma operação para cada mensagem reencaminhada. Por exemplo, enviar uma mensagem para um tópico com 20 subscrições, cada um deles configurados para reencaminhar automaticamente mensagens para outra fila ou tópico, é faturada como 21 operações se todas as subscrições de primeiro nível recebem uma cópia da mensagem.

Para criar uma subscrição que é ligados em para outra fila ou tópico, o criador de blocos da subscrição tem de ter permissões de **Gerir** a origem e a entidade de destino. Apenas enviar mensagens para o tópico de origem requer permissões **Enviar** no tópico de origem.

## <a name="next-steps"></a>Próximos passos

Para obter informações detalhadas sobre o reencaminhamento de automática, consulte os seguintes tópicos de referência:

- [SubscriptionDescription.ForwardTo][]
- [QueueDescription][]
- [SubscriptionDescription][]

Para saber mais sobre melhorias de desempenho Bus de serviço, consulte o artigo [Partitioned entidades de mensagens][].

  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [SubscriptionDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.forwardto.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [SubscriptionDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.aspx
  [0]: ./media/service-bus-auto-forwarding/IC628631.gif
  [1]: ./media/service-bus-auto-forwarding/IC628632.gif
  [Entidades de mensagens com partições]: service-bus-partitioning.md