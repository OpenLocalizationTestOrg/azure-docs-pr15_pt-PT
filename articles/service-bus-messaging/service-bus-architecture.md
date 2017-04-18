<properties 
    pageTitle="Arquitetura de serviço Bus | Microsoft Azure"
    description="Descreve a mensagem e reencaminhamento arquitectura do Azure Service Bus de processamento."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/11/2016"
    ms.author="sethm" />

# <a name="service-bus-architecture"></a>Arquitetura de Bus de serviço

Este artigo descreve a mensagem e reencaminhamento arquitectura do Azure Service Bus de processamento.

## <a name="service-bus-scale-units"></a>Unidades da escala Bus de serviço

Serviço Bus está organizado por *unidades da escala*. Uma unidade de escala é uma unidade de implementação e contém todos os componentes necessários executar o serviço. Cada região implementa um ou mais unidades de escala Bus de serviço.

Um espaço de nomes de serviço Bus é mapeado para uma unidade de escala. A unidade de escala processa todos os tipos de entidades Bus de serviço: estações de retransmissão e entidades mensagens juntamente (filas, tópicos, subscrições). Uma unidade de escala de serviço Bus é constituído pelos seguintes componentes:

- **Um conjunto de nós de gateway.** Nós de gateway autenticar pedidos recebidos e gerir pedidos de reencaminhamento. Cada nó de gateway tem um endereço IP público.

- **Um conjunto de mensagens corretor nós.** Mensagens nós de corretor processam pedidos de entidades de mensagens.

- **Arquivo de um gateway.** Arquivo de gateway contém os dados para cada entidade que está definido neste unidade de escala. O arquivo de gateway é implementado na parte superior de uma base de dados do SQL Azure.

- **Múltiplos arquivos de mensagens.** Armazena mensagens mantenha as mensagens de todas as filas, tópicos e subscrições que são definidas neste unidade de escala. Também contém todos os dados de subscrição. A menos que esteja ativada [com a partições entidades de mensagens](service-bus-partitioning.md) , uma fila ou tópico é mapeado para um arquivo de mensagens. Subscrições são armazenadas no arquivo de mensagens do mesmo como os respetivos tópico principal. Exceto Bus de serviço [De mensagens Premium](service-bus-premium-messaging.md), o armazena mensagens é implementado na parte superior de bases de dados do SQL Azure.

## <a name="containers"></a>Contentores

Cada entidade mensagens é atribuída um contentor específico. Um contentor é uma construção lógica que utiliza um arquivo para o arquivo de mensagens todos os dados relevantes para este contentor. Cada contentor está atribuído a um nó corretor mensagens. Normalmente, existem vários contentores que nós corretor de mensagens. Por conseguinte, cada nó corretor mensagens carrega contentores múltiplos. A distribuição dos contentores para um nó corretor mensagens está organizada assim que todos os nós corretor mensagens quatros são carregados. Se a carga padrão alterações (por exemplo, uma das obtém contentores muito ocupado) ou, se um nó corretor mensagens torna-se temporariamente indisponível, os contentores são redistribuídos pelos nós corretor mensagens.

## <a name="processing-of-incoming-messaging-requests"></a>Processamento de pedidos de mensagens recebidos

Quando um cliente envia um pedido de serviço bus, Balanceador de carga Azure encaminha-o para qualquer um de nós do gateway. O nó do gateway autoriza o pedido. Se o pedido se refira uma entidade de mensagens (fila de espera, tópico, subscrição), o nó do gateway procura a entidade no arquivo de gateway e determina no qual arquivo de mensagens está localizada a entidade. Aspeto, em seguida, o qual o nó corretor mensagens está atualmente a assistir neste contentor e envia o pedido para esse nó corretor mensagens. O nó corretor mensagens processa o pedido e atualiza o estado de entidade na loja contentor. O nó corretor mensagens envia, em seguida, a resposta para o nó do gateway, envia uma resposta adequada volta para o cliente que emitiu o pedido original.

![Processamento de pedidos de mensagens recebidos](./media/service-bus-architecture/IC690644.png)

## <a name="processing-of-incoming-relay-requests"></a>Processamento de pedidos recebidos de reencaminhamento

Quando um cliente envia um pedido de serviço bus, Balanceador de carga Azure encaminha-o para qualquer um de nós do gateway. Se o pedido for um pedido de espera, o nó do gateway cria um novo reencaminhamento. Se o pedido for um pedido de ligação para um reencaminhamento específico, o nó do gateway reencaminha o pedido de ligação para o nó do gateway proprietária o reencaminhamento. O nó do gateway proprietária o reencaminhamento envia um pedido de rendezvous para o cliente de espera, perguntar escuta para criar um canal para o nó do gateway que recebeu o pedido de ligação temporário.

Quando é estabelecida a ligação de reencaminhamento, os clientes podem trocar mensagens através do nó do gateway utilizado para o rendezvous.

![Processamento de pedidos recebidos de reencaminhamento](./media/service-bus-architecture/IC690645.png)

## <a name="next-steps"></a>Próximos passos

Agora que já leu uma descrição geral de arquitectura Bus de serviço, para começar a utilizar visite as ligações seguintes:

- [Descrição geral do serviço Bus messaging](service-bus-messaging-overview.md)
- [Conceitos básicos da Bus de serviço](service-bus-fundamentals-hybrid-solutions.md)
- [Uma solução de mensagens em fila utilizando filas Bus de serviço](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
