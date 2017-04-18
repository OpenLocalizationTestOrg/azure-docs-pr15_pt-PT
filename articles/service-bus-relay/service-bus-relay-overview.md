<properties
    pageTitle="Descrição geral do reencaminhamento de serviço Bus | Microsoft Azure"
    description="Descrição geral do reencaminhamento de Bus de serviço."
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
    ms.date="09/01/2016"
    ms.author="sethm"/>


# <a name="overview-of-service-bus-relay"></a>Descrição geral do reencaminhamento de Bus de serviço

Um componente principal do serviço Bus é um serviço de centralizada (mas altamente balanceamento de carga) *reencaminhamento* que permite-lhe criar aplicações híbridas que são executados num centro de dados do Azure e no seu ambiente de empresa no local.  O reencaminhamento de serviço Bus suporta uma variedade de diferentes protocolos de transporte e padrões de serviços web. Isto inclui SOAP WS-, * e até mesmo restantes. O serviço de reencaminhamento facilita a suas aplicações híbridas, permitindo-lhe para expor segura serviços do Windows Communication Foundation (WCF) que se encontram dentro de uma rede de empresa da empresa para a nuvem pública, sem ter de abrir uma ligação de firewall ou exigem alterações intrusivas a uma infraestrutura de rede da empresa. 

![Conceitos de reencaminhamento](./media/service-bus-relay-overview/sb-relay-01.png)

O serviço de reencaminhamento suporta tradicional unidirecional mensagens, pedido/resposta mensagens e ponto a ponto mensagens. Também suporta a distribuição evento internet-âmbito ativar cenários publicar/subscrever e comunicação bidirecional socket para eficiência de ponto a ponto maior. 

No transmitido mensagens padrão, um serviço no local liga para o serviço de reencaminhamento através de uma porta de saída e cria um socket bidirecional para comunicações associados a um endereço de rendezvous específico. O cliente, em seguida, pode comunicar com o serviço no local ao enviar mensagens para o serviço de reencaminhamento filtragem o endereço de rendezvous. O serviço de reencaminhamento irá, em seguida, "reencaminhar" mensagens para o serviço no local através do socket bidirecional já no local. O cliente não necessita de uma ligação direta para o serviço no local, não é necessário saber onde reside o serviço e o serviço no local não precisam de qualquer portas de entrada aberta na firewall.

Iniciar a ligação entre o seu serviço no local e o serviço de reencaminhamento utilizando um conjunto de enlaces reencaminhamento"WCF". Nos bastidores, as associações de reencaminhamento mapear para novos elementos de encadernação de transporte foi concebidos para criar componentes de canal WCF que se integram com Bus serviço na nuvem. 

## <a name="next-steps"></a>Próximos passos

Para obter detalhes sobre o reencaminhamento de Bus de serviço, consulte os tópicos seguintes.

- [Descrição geral de arquitectura de Bus do serviço Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [Como utilizar o serviço de reencaminhamento de Bus de serviço](service-bus-dotnet-how-to-use-relay.md)

 