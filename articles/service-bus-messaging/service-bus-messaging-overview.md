<properties
    pageTitle="Descrição geral do serviço Bus messaging | Microsoft Azure"
    description="Serviço Bus Messaging: entrega de dados flexíveis na nuvem"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="sethm"/>


# <a name="service-bus-messaging-flexible-data-delivery-in-the-cloud"></a>Bus de serviço de mensagens: entrega dos dados flexíveis na nuvem

Microsoft Azure Service Bus é um serviço de entrega de informações fiáveis. É o objetivo deste serviço facilitar a comunicação. Quando pretenderem duas ou mais partes obter informações do exchange, que necessitam de um mecanismo de comunicação. Serviço Bus é um mecanismo de comunicação juntamente ou por terceiros. Este é semelhante a um serviço de postal no mundo físico. Serviços postais facilitam muito enviar diferentes tipos de letras e pacotes com uma variedade de entrega garantias, qualquer parte do mundo.

Semelhante ao serviço de postais faz letras, o serviço Bus é entrega informações flexível a partir do remetente e o destinatário. O serviço de mensagens garante que as informações são entregues mesmo se as duas partes nunca estão ambas as online ao mesmo tempo, ou se estes não estão disponíveis ao mesmo tempo exato. Desta forma, de mensagens são semelhante ao enviar uma carta, enquanto não controlada comunicação é semelhante a colocar uma chamada telefónica (ou como uma chamada telefónica utilizado para ser - antes de chamada em espera e autor da chamada ID, que são muito mais, como mensagens juntamente).

O remetente da mensagem, também pode pedir uma variedade de características de entrega, incluindo as transações, deteção de duplicados, com base no tempo de expiração e de lotes. Os padrões de ter analogias postais, assim: Repetir entrega, assinatura necessária, alteração de endereço ou resgate.

Serviço Bus suporta dois padrões de mensagens distintas: *reencaminhamento* e *juntamente mensagens*.

## <a name="service-bus-relay"></a>Serviço Bus reencaminhamento

O componente de [reencaminhamento](../service-bus-relay/service-bus-relay-overview.md) do serviço Bus é um serviço de centralizada (mas altamente balanceamento de carga) que suporta uma variedade de diferentes protocolos de transporte e padrões de serviços Web. Isto inclui SOAP WS-, * e até mesmo restantes. O [serviço de reencaminhamento](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md) fornece uma variedade de opções de conectividade de reencaminhamento diferente e pode ajudar a negoceie diretas ligações de ponto a ponto quando é possível. Serviço Bus está optimizada para programadores do .NET que utilizam o Windows Communication Foundation (WCF), ambos no que diz respeito à e de desempenho e fornece acesso total ao serviço de reencaminhamento através de interfaces SOAP e coloque o PONTEIRO. Isto torna possível para qualquer SOAP ou o resto ambiente de programação para integrar com o serviço Bus.

O serviço de reencaminhamento suporta tradicional unidirecional mensagens, pedido/resposta mensagens e ponto a ponto mensagens. Também suporta âmbito de Internet para ativar a distribuição evento publicar-subscrever cenários e comunicação bidirecional socket para eficiência de ponto a ponto maior. No transmitido mensagens padrão, um serviço no local liga para o serviço de reencaminhamento através de uma porta de saída e cria um socket bidirecional para comunicações associados a um endereço de rendezvous específico. O cliente, em seguida, pode comunicar com o serviço no local ao enviar mensagens para o serviço de reencaminhamento filtragem o endereço de rendezvous. O serviço de reencaminhamento irá, em seguida, "reencaminhar" mensagens para o serviço no local através do socket bidirecional já no local. O cliente não necessita de uma ligação direta para o serviço no local, nem é necessário saber onde reside o serviço e o serviço no local não precisam de qualquer portas de entrada aberta na firewall.

Iniciar a ligação entre o seu serviço no local e o serviço de reencaminhamento, utilizando um conjunto de enlaces reencaminhamento"WCF". Nos bastidores, as associações de reencaminhamento mapeiam para transporte elementos enlace foi concebidos para criar componentes de canal WCF que se integram com Bus serviço na nuvem.

Serviço Bus reencaminhamento fornece várias vantagens, mas requer que o servidor e cliente para ambos estar online ao mesmo tempo para poder enviar e receber mensagens. Não é ideal para a comunicação de estilo de HTTP, na qual os pedidos de podem não ser normalmente longa vida, nem para clientes que ligar-se apenas ocasionalmente, tal como browsers, aplicações móveis e assim sucessivamente. Controlada mensagens suporta desacoplados comunicação e tem a suas própria vantagens; os clientes e servidores podem ligar-se quando for necessário e executar as operações de uma forma assíncrona.

## <a name="brokered-messaging"></a>Controlada mensagens

Contrariamente ao esquema reencaminhamento, [juntamente mensagens](service-bus-queues-topics-subscriptions.md) pode ser considerado como assíncrona ou "amplificada desacoplado." Produtor (remetentes) e consumidores (destinatários) não tem de estar online ao mesmo tempo. A infraestrutura de mensagens com fiabilidade armazena as mensagens no "corretor" (tal como uma fila) até à parte consome está pronta a recebê-las. Esta opção permite-os componentes da aplicação distribuída para ser desligada, quer imediatamente; Por exemplo, para a manutenção ou devido a uma falha de sistema do componente, sem afetar a totalidade do sistema. Além disso, a aplicação de recepção apenas poderá ter de ficar online durante determinadas horas do dia, tal como um sistema de gestão de inventário apenas é necessária para executar no fim do dia empresas.

Os componentes principais a infraestrutura de mensagens juntamente do serviço Bus são filas, tópicos e subscrições.  A diferença principal é que tópicos suportam funcionalidades publicar/subscrever que podem ser utilizadas para sofisticadas com base no conteúdo encaminhamento e a entrega lógica, incluindo o envio a vários destinatários. Estes componentes Ativar novos cenários de mensagens assíncronos, tal como desacoplamento temporal, publiquem/subscrever e balanceamento de carga. Para mais informações sobre estas entidades de mensagens, consulte [serviço Bus filas, tópicos e subscrições](service-bus-queues-topics-subscriptions.md).

Tal como acontece com a infraestrutura de reencaminhamento, a função de mensagens juntamente é fornecida para programadores WCF e o .NET Framework e através do resto.

## <a name="next-steps"></a>Próximos passos

Para saber mais acerca das mensagens Bus de serviço, consulte os tópicos seguintes.

- [Conceitos básicos da Bus de serviço](service-bus-fundamentals-hybrid-solutions.md)
- [Serviço Bus filas, tópicos e subscrições](service-bus-queues-topics-subscriptions.md)
- [Como utilizar filas Bus de serviço](service-bus-dotnet-get-started-with-queues.md)
- [Como utilizar a subscrições e tópicos de Bus de serviço](./service-bus-dotnet-how-to-use-topics-subscriptions.md)
 
