<properties 
    pageTitle="Aplicações de serviço Bus contra de corrente e catástrofes de isolamento | Microsoft Azure"
    description="Descreve técnicas que pode utilizar para proteger aplicações contra uma falha de serviço Bus potencial."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/02/2016"
    ms.author="sethm" />

# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Práticas recomendadas para aplicações contra falhas de serviço Bus e catástrofes de isolamento

Aplicações críticos devem funcionar continuamente, mesmo em presença não planeadas falhas ou catástrofes. Este tópico descreve técnicas que pode utilizar para proteger as aplicações de serviço Bus relativamente a um potencial indisponibilidade do serviço ou falhas.

Uma falha de é definida como a indisponibilidade temporária do Azure Service Bus. A indisponibilidade pode afetar alguns componentes do serviço Bus, tal como um arquivo de mensagens ou mesmo todo o Centro de dados. Depois do problema foi resolvido, serviço Bus fica disponível novamente. Normalmente, uma falha de não causar a perda de mensagens ou outros dados. Um exemplo de falha de um componente é a indisponibilidade de um determinado arquivo de mensagens. Um exemplo de uma falha ao nível do Centro de dados é uma falha de energia do Centro de dados ou um parâmetro de rede do Centro de dados com problemas. Pode últimos uma falha de alguns minutos para alguns dias.

Uma falhas são definida como a perda permanente de uma unidade de escala de serviço Bus ou o Centro de dados. O Centro de dados pode ou pode não ficar disponível novamente. Normalmente, uma falhas faz com que perda de algumas ou todas as mensagens ou outros dados. Exemplos de catástrofes são fire, transbordo ou terramoto.

## <a name="current-architecture"></a>Arquitetura atual

Serviço Bus utiliza vários arquivos de mensagens para armazenar as mensagens enviadas para filas ou tópicos. Um sem partições fila ou um tópico é atribuído a um arquivo de mensagens. Se este arquivo de mensagens não estiver disponível, todas as operações nessa fila ou tópico irão falhar.

Todas as entidades de mensagens Bus de serviço (filas, tópicos, estações de retransmissão) residem no espaço de nomes de serviço, o qual está inscrito com um centro de dados. Serviço Bus não ativar replicação de geo automática de dados, nem disponibiliza um espaço de nomes abranger vários centros de dados.

## <a name="protecting-against-acs-outages"></a>Proteger contra falhas ACS

Se estiver a utilizar o credenciais ACS e ACS torna-se indisponível, clientes já não podem obter tokens. Os clientes que têm um token ao tempo que vai ACS para baixo podem continuar a utilizar o serviço Bus até que os tokens de expirarem. A duração do token predefinição é 3 horas.

Para proteger contra falhas ACS, utilize tokens de assinatura partilhadas do Access (SA). Neste caso, o cliente autentica diretamente com Bus de serviço ao iniciar um token minted personalizada com uma chave secreta. As chamadas para ACS já não são necessárias. Para mais informações sobre tokens SA, consulte o artigo [Bus de serviço de autenticação][].

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Proteger filas e tópicos contra falhas de arquivo de mensagens

Um sem partições fila ou um tópico é atribuído a um arquivo de mensagens. Se este arquivo de mensagens não estiver disponível, todas as operações nessa fila ou tópico irão falhar. Uma fila com partições, é composta por outro lado, vários fragmentos. Cada fragmento é armazenado num arquivo de mensagens diferentes. Quando uma mensagem é enviada para uma fila com partições ou tópico, o serviço Bus atribui a mensagem para um dos fragmentos. Se o arquivo de mensagens correspondente não estiver disponível, serviço Bus escreve a mensagem para um fragmento diferente, se possível. Para mais informações sobre as entidades com partições, consulte a [partições entidades de mensagens][].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Proteger contra falhas de centro de dados ou catástrofes

Para permitir uma activação pós-falha entre dois centros de dados, pode criar um espaço de nomes de serviço do serviço Bus em cada centro de dados. Por exemplo, o de espaço de nomes de serviço do serviço Bus **contosoPrimary.servicebus.windows.net** podem estar localizados na região dos Estados Unidos América do Norte/Central e **contosoSecondary.servicebus.windows.net** podem estar localizados na região -nos Sul/Central. Se um Bus de serviço de mensagens entidade tem de permanecer acessível na presença de uma falha do Centro de dados, pode criar essa entidade em ambos os espaços de nomes.

Para mais informações, consulte a secção "Falha do serviço Bus dentro de um centro de dados do Azure" no [assíncronas padrões de mensagens e disponibilidade de alta][].

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Proteger os pontos finais de reencaminhamento contra falhas de centro de dados ou catástrofes

A replicação geo dos pontos finais de relay permite que um serviço que expõe um ponto final de reencaminhamento para ser acessível na presença de corrente Bus de serviço. Para alcançar geo replicação, o serviço tem de criar dois pontos finais de reencaminhamento no diferentes espaços de nomes. Tem residem os espaços de nomes na centros de dados diferentes e os dois pontos finais tem de ter nomes diferentes. Por exemplo, um ponto final principal possa ser contactado em **contosoPrimary.servicebus.windows.net/myPrimaryService**, enquanto o homólogo para secundário possa ser contactado em **contosoSecondary.servicebus.windows.net/mySecondaryService**.

O serviço, em seguida, recebe em ambos os pontos finais, e um cliente pode chamar o serviço de através de um dos ponto final. Uma aplicação de cliente aleatoriamente escolhe um da retransmissão como o ponto final principal e envia o seu pedido para o ponto final ativo. Se a operação falhar com um código de erro, esta falha indica que o ponto final de reencaminhamento não está disponível. A aplicação é aberto um canal para o ponto final de cópia de segurança e volta a publicar o pedido. Essa activo e os pontos finais da cópia de segurança mudam funções: a aplicação de cliente leva em consideração o antigo ponto final de ativo para ser o novo ponto final de cópia de segurança e o antigo ponto final de cópia de segurança para ser o novo ponto final ativo. Se ambos enviar operações falhar, as funções das duas entidades que permaneça inalteradas e é devolvido um erro.

O exemplo [a replicação Geo com serviço Bus reencaminhado mensagens][] demonstra como criar uma réplica estações de retransmissão.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Proteger filas e tópicos contra falhas de centro de dados ou catástrofes

Para alcançar resistência contra falhas de centro de dados quando utilizar controlada mensagens, o serviço Bus suporta duas abordagens: replicação *ativo* e *passivo* . Para cada abordagem, se um determinado fila ou um tópico tem de permanecer acessível na presença de uma falha do Centro de dados, pode criá-lo em ambos os espaços de nomes. Ambas as entidades podem ter o mesmo nome. Por exemplo, uma fila primária possa ser contactada em **contosoPrimary.servicebus.windows.net/myQueue**, enquanto o homólogo para secundário possa ser contactado em **contosoSecondary.servicebus.windows.net/myQueue**.

Se a aplicação não necessita de comunicação de remetente para recetor permanente, a aplicação pode implementar uma fila resistente do lado do cliente para evitar a perda de mensagem e para proteger o remetente a partir de qualquer erros de serviço Bus breves.

## <a name="active-replication"></a>Replicação do Active

A replicação do Active utiliza entidades em ambos os espaços de nomes para cada operação. Qualquer cliente que envia uma mensagem envia duas cópias da mesma mensagem. A primeira cópia é enviada para a entidade primária (por exemplo, **contosoPrimary.servicebus.windows.net/sales**) e, a segunda cópia da mensagem é enviada para a entidade secundária (por exemplo, **contosoSecondary.servicebus.windows.net/sales**).

Um cliente recebe mensagens de ambas as filas. No auscultador processa a primeira cópia de uma mensagem e a segunda cópia é suprimir. Para suprimir mensagens duplicadas, o remetente tem de marcar cada mensagem com um identificador exclusivo. Ambas as cópias da mensagem devem ser marcadas com o mesmo identificador. Pode utilizar as propriedades [BrokeredMessage.MessageId][] ou [BrokeredMessage.Label][] ou uma propriedade personalizada para marcar a mensagem. No auscultador tem de manter uma lista de mensagens que já tenha recebido.

O exemplo [a replicação Geo com serviço Bus controlada mensagens][] demonstra a replicação do active do messaging entidades.

> [AZURE.NOTE] A abordagem de replicação do active é o número de operações duplicado, por conseguinte, esta abordagem pode levar a custo mais elevado.

## <a name="passive-replication"></a>Replicação passiva

No caso de falha de royalties, replicação passiva utiliza apenas uma das duas entidades mensagens. Um cliente envia a mensagem à entidade ativo. Se a operação na entidade ativa falhar com um código de erro que indica o Centro de dados que aloja a entidade ativa pode não estar disponível, o cliente envia uma cópia da mensagem para a entidade de cópia de segurança. Essa activo e as entidades de cópia de segurança mudam funções: o cliente envio considera a entidade de ativa antiga para a nova entidade de cópia de segurança e, a entidade de cópia de segurança antiga é a nova entidade ativa. Se ambos enviar operações falhar, as funções das duas entidades que permaneça inalteradas e é devolvido um erro.

Um cliente recebe mensagens de ambas as filas. Porque não existe a oportunidade de que o destinatário recebe duas cópias da mesma mensagem, o destinatário tem suprimir mensagens duplicadas. É possível suprimir duplicados da mesma forma, tal como descrito para a replicação ativa.

Em geral, a replicação passiva é mais económica do que a replicação do active porque na maioria dos casos é efetuada a operação apenas uma. Latência, débito e custo monetário são idênticos do cenário de replicadas não.

Quando utilizar a replicação passiva, nos cenários seguintes mensagens podem ser perdidas ou recebidas duas vezes:

-   **Atraso de mensagem ou perda**: partem do princípio de que o remetente enviadas com êxito m1 uma mensagem para a fila principal e, em seguida, fila de espera fica indisponível antes do destinatário recebe m1. O remetente envia uma mensagem subsequente m2 para a fila secundária. Se a fila primária está temporariamente indisponível, o destinatário recebe m1 depois de fila de espera fica disponível novamente. Em caso de uma falhas, o destinatário nunca poderá receber m1.

-   **Duplicar recepção**: partem do princípio de que o remetente envia uma mensagem de m para a fila principal. Serviço Bus com êxito processa m mas falha ao enviar uma resposta. Depois da operação de enviar o tempo limite, o remetente envia uma cópia idêntica da m para a fila secundária. Se conseguir receber a primeira cópia do m antes da fila primária torna-se disponível no auscultador, o destinatário recebe ambas as cópias do m aproximadamente ao mesmo tempo. Se o destinatário não for capaz receber a primeira cópia do m antes da fila primária torna-se indisponível, no auscultador inicialmente recebe apenas a segunda cópia do m, mas, em seguida, recebe uma segunda cópia do m quando fila de espera primária fica disponível.

O exemplo [a replicação Geo com serviço Bus controlada mensagens][] demonstra passiva replicação de entidades de mensagens.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre recuperação de falhas, consulte estes artigos:

- [Continuidade de negócios de base de dados do Azure SQL][]
- [Orientação técnica do Azure RDP][]

  [Autenticação do serviço de Bus]: service-bus-authentication-and-authorization.md
  [Entidades de mensagens com partições]: service-bus-partitioning.md
  [Assíncronas padrões de mensagens e elevada disponibilidade]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
  [A replicação geo com serviço Bus reencaminhado mensagens]: http://code.msdn.microsoft.com/Geo-replication-with-16dbfecd
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [BrokeredMessage.Label]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx
  [A replicação geo com serviço Bus controlada mensagens]: http://code.msdn.microsoft.com/Geo-replication-with-f5688664
  [Continuidade de negócios de base de dados do Azure SQL]: ../sql-database/sql-database-business-continuity.md
  [Orientação técnica do Azure RDP]: ../resiliency/resiliency-technical-guidance.md
