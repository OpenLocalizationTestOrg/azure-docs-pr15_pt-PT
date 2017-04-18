<properties
    pageTitle="Serviço Bus Premium e mensagens padrão preços descrição geral de camadas | Microsoft Azure"
    description="Serviço Bus Premium e mensagens padrão"
    services="service-bus"
    documentationCenter=".net"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/02/2016"
    ms.author="darosa;sethm"/>

# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Camadas de mensagens padrão e serviço Bus Premium 

Bus de serviço de mensagens, que inclui mensagens entidades como filas e publiquem-subscrever a semântica de escala de nuvem de tópicos, combina enterprise mensagens capacidades com RTF. Bus de serviço de mensagens são utilizado como a estrutura de comunicação para muitos soluções de nuvem sofisticadas.

A camada *Premium* do serviço Bus messaging endereços comuns pedidos de cliente à volta de escala, desempenho e disponibilidade para aplicações críticos. Apesar dos conjuntos de funcionalidades são praticamente idênticos, estas duas camadas do serviço Bus messaging foram concebidas para servir casos de utilização de diferentes.

Algumas diferenças de alto nível estão realçadas na tabela abaixo.

| Premium                               | Padrão                       |
|---------------------------------------|--------------------------------|
| Alto débito                       | Débito variável            |
| Desempenho previsível               | Latência variável               |
| Preços previsíveis                   | Colar como pague variável preços |
| Capacidade para dimensionar para carga de trabalho | N/D                            |
| > 256KB de tamanho de mensagem                  | Tamanho da mensagem é 256KB          |

**Serviço Bus Premium mensagens** fornece isolamento de recursos na camada de memória e CPU para que cada carga de trabalho do cliente é executado no isolamento. Neste contentor recurso é denominado uma *unidade de mensagens*. O que cada espaço de nomes de premium é atribuído, pelo menos, uma unidade de mensagens. Pode comprar 1, 2 ou 4 unidades mensagens para cada espaço de nomes de serviço Bus Premium. Uma única carga de trabalho ou de uma entidade pode abranger múltiplas unidades de mensagens e o número de unidades mensagens pode ser alterado aleatoriamente, apesar de faturação está em taxas de taxa de 24 horas ou diariamente. O resultado é desempenho previsível e forma repetido para a sua solução baseada em Bus de serviço.

Não só é este desempenho mais previsíveis e disponível, mas também é mais rápido. Mensagens de serviço Bus Premium constrói do motor de armazenamento introduzido numa [Azure evento concentradores](https://azure.microsoft.com/services/event-hubs/)de dados. Com mensagens Premium, o desempenho de pico é muito mais rapidamente com a camada padrão.

## <a name="premium-messaging-technical-differences"></a>Diferenças de técnicas de mensagens de Premium

Seguem-se algumas diferenças entre Premium e camadas de mensagens padrão.

### <a name="partitioned-queues-and-topics"></a>Filas com partições e tópicos

Filas com partições e tópicos são suportados no Premium mensagens, mas não funcionam da mesma forma, tal como as camadas padrão e Basic do serviço Bus messaging. Premium mensagens não utilizam o SQL como um arquivo de dados e já não está a concorrência recurso possíveis associada uma plataforma partilhada. Como resultado, partições não não necessário. Para além disso, a contagem de partição ter sido alterada a partir de 16 partições no mensagens padrão a 2 partições no Premium. Está a ter duas partições garante a disponibilidade e é um número mais adequado para o ambiente de runtime Premium. Para mais informações sobre como criar partições, consulte o artigo [Partitioned filas e tópicos](service-bus-partitioning.md).

### <a name="express-entities"></a>Express entidades

Porque é executada Premium mensagens num ambiente runtime completamente isolados, express entidades não são suportadas em Premium espaços de nomes. Para mais informações sobre a funcionalidade express, consulte a propriedade [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx) .

## <a name="next-steps"></a>Próximos passos

Para saber mais acerca das mensagens Bus de serviço, consulte os tópicos seguintes.

- [Introdução ao Azure Service Bus Premium mensagens (blogue)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introdução ao Azure Service Bus Premium mensagens (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Descrição geral do serviço Bus messaging](service-bus-messaging-overview.md)
- [Como utilizar filas Bus de serviço](service-bus-dotnet-get-started-with-queues.md)
