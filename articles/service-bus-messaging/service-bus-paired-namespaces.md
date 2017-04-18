<properties 
    pageTitle="Serviço Bus emparelhadas espaços de nomes | Microsoft Azure"
    description="Detalhes de implementação do espaço de nomes emparelhado e custo"
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
    ms.date="10/04/2016"
    ms.author="sethm" />

# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Emparelhadas detalhes de implementação do espaço de nomes e implicações de custos

O método de [PairNamespaceAsync][] , utilizando uma instância de [SendAvailabilityPairedNamespaceOptions][] , executa tarefas visíveis em seu nome. Uma vez que não existem são o custo considerações ao utilizar a funcionalidade, é útil para compreender essas tarefas, para que o que esperar o comportamento quando isto acontecer. API realiza o seguinte comportamento automático em seu nome:

-   Criação de registo de tarefas pendentes filas.
-   Criação de um objeto de [MessageSender][] comunica filas ou tópicos.
-   Quando uma entidade mensagens fica indisponível, envia ping mensagens à entidade uma tentativa para detetar quando essa entidade fica disponível novamente.
-   Cria, opcionalmente, de um conjunto de "bombas mensagem" que mover mensagens as filas de registo de tarefas pendentes para as filas principais.
-   Coordenadas fecho/provocar uma falha de instâncias de [MessagingFactory][] principais e secundárias.

De alto nível, a funcionalidade funciona: quando a entidade primária está em bom estada, não comportamento ocorrem alterações. Quando vê não tiver êxito a duração de [FailoverInterval][] decorre e a entidade primária envia depois de um não transitório [MessagingException][] ou um [TimeoutException][], ocorre o seguinte comportamento:

1.  Envie operações à entidade primária são desativadas e o sistema de ping entidade primária até pings podem ser entregues com êxito.

2.  Uma fila de registo de tarefas pendentes aleatório está selecionada.

3.  Objetos de [BrokeredMessage][] são encaminhados para a fila de registo de tarefas pendentes que selecionou.

1.  Se uma operação de enviar para a fila de registo de tarefas pendentes que selecionou falha, é movida essa fila da rotação e uma nova fila está selecionada. Todos os remetentes na instância [MessagingFactory][] saiba da falha.

Os algarismos seguintes representar a sequência. Em primeiro lugar, o remetente envia mensagens.

![Espaços de nomes de pares][0]

Após uma falha ao enviar para a fila principal, o remetente começa enviar mensagens para uma fila de registo de tarefas pendentes aleatoriamente que selecionou. Em simultâneo, desde o início uma tarefa de ping.

![Espaços de nomes de pares][1]

Neste momento, as mensagens ainda sejam encontram na fila de espera secundária e não foram entregues para a fila principal. Assim que a fila primária está em bom estada novamente, pelo menos um processo deverá estar em execução a sifão. O sifão fornece as mensagens de todas as filas vários do registo de tarefas pendentes para as entidades de destino inicial maiúscula (filas e tópicos).

![Espaços de nomes de pares][2]

O restante deste tópico aborda os detalhes específicos de como estas peças funcionam.

## <a name="creation-of-backlog-queues"></a>Criação de filas de registo de tarefas pendentes

O objeto [SendAvailabilityPairedNamespaceOptions][] passado para o método de [PairNamespaceAsync][] indica o número de registo de tarefas pendentes filas que pretende utilizar. Cada fila de registo de tarefas pendentes, em seguida, é criada com as seguintes propriedades explicitamente conjunto (todos os outros valores são definidos para as predefinições do [QueueDescription][] ):

| Caminho                                   | [espaço de nomes primário] / transferência de servicebus x / [indexar] onde [index] é um valor no [0, BacklogQueueCount) |
|----------------------------------------|------------------------------------------------------------------------------------------------------|
| MaxSizeInMegabytes                     | 5120                                                                                                 |
| MaxDeliveryCount                       | Int. Ao MaxValue                                                                                         |
| DefaultMessageTimeToLive               | MaxValue                                                                                    |
| AutoDeleteOnIdle                       | MaxValue                                                                                    |
| LockDuration                           | 1 minuto                                                                                             |
| EnableDeadLetteringOnMessageExpiration | VERDADEIRO                                                                                                 |
| EnableBatchedOperations                | VERDADEIRO                                                                                                 |

Por exemplo, a primeira fila de registo de tarefas pendentes criados para o espaço de nomes **contoso** se chamar `contoso/x-servicebus-transfer/0`.

Quando criar as filas, o código pela primeira vez verifica se existe uma fila de espera. Se não existir fila de espera, a fila é criada. O código não limpar filas de registo de tarefas pendentes "extra". Especificamente, se a aplicação com o espaço de nomes principal **contoso** solicitar cinco registo de tarefas pendentes filas mas uma fila de registo de tarefas pendentes com o caminho `contoso/x-servicebus-transfer/7` existir, essa fila de registo de tarefas pendentes extra ainda estiver presente mas não é utilizada. O sistema permite explicitamente filas de registo de tarefas pendentes extra existir que não seriam utilizadas. Como o proprietário do espaço de nomes, é responsável para limpar quaisquer filas de registo de tarefas pendentes não utilizadas/indesejados. O motivo para esta decisão é que Bus de serviço não é possível saber que efeitos existem para todas as filas no seu espaço de nomes. Além disso, se uma fila existe com o nome próprio, mas não cumpre os assumido [QueueDescription][], em seguida, os motivos são suas próprias para alterar o comportamento predefinido. Sem garantias forem efetuadas alterações às filas de registo de tarefas pendentes pelo seu código. Certifique-se testar cuidadosamente as suas alterações.

## <a name="custom-messagesender"></a>MessageSender personalizado

Quando a enviar, todas as mensagens aceda através de um objeto de [MessageSender][] interno que comporta-se normalmente quando tudo funciona e redireciona para as filas de registo de tarefas pendentes quando coisas "quebra." Ao receber uma falha de não transitório, um temporizador é iniciado. Após um período de [TimeSpan][] que consiste o valor da propriedade [FailoverInterval][] durante o qual não tiver êxito são enviadas mensagens, a activação pós-falha é interessada. Neste momento, as seguintes coisas ocorrerem para cada entidade:

- Uma tarefa de ping executa cada [PingPrimaryInterval][] para verificar se a entidade está disponível. Assim que esta tarefa ser bem sucedida, todos os códigos de cliente que utiliza a entidade imediatamente inicia enviar novas mensagens para o espaço de nomes principal.

- Pedidos de futuros para enviar a essa mesma entidade a partir de qualquer outros remetentes irão resultar no [BrokeredMessage][] a ser enviada para ser modificadas sentar na fila de registo de tarefas pendentes. A modificação remove algumas propriedades do objeto [BrokeredMessage][] e armazena-los noutro local. As seguintes propriedades são desmarcadas e adicionadas num novo alias, permitindo Bus de serviço e o SDK processar uniformemente mensagens:

| Nome da propriedade antigo       | Novo nome de propriedade |
|-------------------------|-------------------|
| ID de sessão               | x-ms-ID de sessão    |
| TimeToLive              | x-ms-timetolive   |
| ScheduledEnqueueTimeUtc | caminho do ms x         |

O caminho de destino original também é armazenado na mensagem como uma propriedade denominada x-ms-path. Este modelo permite que as mensagens para várias entidades de coexistência na fila único registo de tarefas pendentes. As propriedades são convertidas novamente à sifão.

O objeto [MessageSender][] personalizado pode deparar-se com problemas quando as mensagens abordagem o limite de 256 KB e activação pós-falha é interessada. O objeto [MessageSender][] personalizado armazena as mensagens para todas as filas e tópicos em conjunto em filas de registo de tarefas pendentes. Este objeto misturas mensagens a partir de muitos primários da agrupados dentro as filas de registo de tarefas pendentes. Para processar balanceamento de carga entre muitos clientes que não sabem entre si, o SDK escolhe aleatoriamente uma fila de registo de tarefas pendentes para cada [QueueClient][] ou [TopicClient][] criar no código.

## <a name="pings"></a>Pings

Uma mensagem de ping é uma vazia [BrokeredMessage][] com a respetiva propriedade [ContentType][] definida para aplicação/vnd.ms-servicebus-ping e um valor de [TimeToLive][] de 1 segundo. Este ping tenha uma característica especial no serviço Bus: o servidor nunca fornece um ping quando um [BrokeredMessage][]os pedidos de qualquer autor da chamada. Assim sendo, que nunca tem que aprender a receber e ignorar estas mensagens. Cada entidade (fila exclusiva ou tópico) por [MessagingFactory][] instância por cliente vai ser tiverem ping quando são considerados não estar disponível. Por predefinição, isto acontece uma vez por minuto. As mensagens do ping são considerados como mensagens de serviço Bus regulares e podem resultar em custos de largura de banda e mensagens. Assim que os clientes detectam que o sistema estiver disponível, as mensagens parar.

## <a name="the-syphon"></a>O sifão

Pelo menos um programa executável na aplicação deverá estar ativamente a executar o sifão. O sifão executa um valor longo inquérito receber que dura 15 minutos. Quando todas as entidades estão disponíveis e tiver 10 filas de registo de tarefas pendentes, a aplicação que aloja a sifão chamadas a operação de receção 40 horas por hora, 960 horas por dia em vezes 28800 30 dias. Quando o sifão ativamente é mover mensagens a partir do registo de tarefas pendentes para a fila principal, cada mensagem tem os seguintes encargos (aplicadas taxas de padrão para o tamanho da mensagem e a largura de banda em todas as fases):

1.  Envie para o registo de tarefas pendentes.

2.  Receba a partir do registo de segurança.

3.  Envie para a página principal.

4.  Receba da página principal.

## <a name="closefault-behavior"></a>Fechar/falhas comportamento

Dentro de uma aplicação que aloja sifão, uma vez as falhas de [MessagingFactory][] principais ou secundárias ou estiver fechada sem o seu parceiro também a ser falhadas/fechada e o sifão Deteta este estado, os factos sifão. Se não estiver fechado o [MessagingFactory][] em 5 segundos, a sifão irá falhas de [MessagingFactory][]continua aberto.

## <a name="next-steps"></a>Próximos passos

Consulte o artigo [assíncronas padrões de mensagens e elevada disponibilidade][] para um debate detalhado do serviço Bus assíncrona messaging. 

  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [FailoverInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.failoverinterval.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
  [PingPrimaryInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.pingprimaryinterval.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [ContentType]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx
  [TimeToLive]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
  [Assíncronas padrões de mensagens e elevada disponibilidade]: service-bus-async-messaging.md
  [0]: ./media/service-bus-paired-namespaces/IC673405.png
  [1]: ./media/service-bus-paired-namespaces/IC673406.png
  [2]: ./media/service-bus-paired-namespaces/IC673407.png
