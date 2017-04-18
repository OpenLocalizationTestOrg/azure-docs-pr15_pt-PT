<properties
    pageTitle="O que é o Azure reencaminhamento? | Microsoft Azure"
    description="Descrição geral do reencaminhamento do Azure"
    services="service-bus"
    documentationCenter=".net"
    authors="banisadr"
    manager="timlt"
    editor="" />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="babanisa" />

# <a name="what-is-azure-relay"></a>O que é o Azure reencaminhamento?

Serviço de reencaminhamento Azure facilita a suas aplicações híbridas, permitindo-lhe para expor segura serviços que se encontram dentro de uma rede de empresa da empresa para a nuvem pública, sem ter de abrir uma ligação de firewall ou exigem alterações intrusivas a uma infraestrutura de rede da empresa. Reencaminhamento suporta uma variedade de diferentes protocolos de transporte e padrões de serviços web.

O serviço de reencaminhamento suporta unidirecional, tradicional pedido de resposta e o tráfego de ponto a ponto. Também suporta a distribuição evento internet-âmbito ativar cenários publicar/subscrever e comunicação bidirecional socket para eficiência de ponto a ponto maior. 

Na padrão de transferência transmitido dados, um serviço no local liga-se para o serviço de reencaminhamento através de uma porta de saída e cria um socket bidirecional para comunicações associados a um endereço de rendezvous específico. O cliente, em seguida, pode comunicar com o serviço no local ao enviar tráfego para o serviço de reencaminhamento filtragem o endereço de rendezvous. O serviço de reencaminhamento irá, em seguida, "reencaminhar" dados para o serviço no local através de um socket bidirecional dedicado a cada cliente. O cliente não necessita de uma ligação direta para o serviço no local, não é necessário saber onde reside o serviço e o serviço no local não precisam de qualquer portas de entrada aberta na firewall.

Os elementos de capacidade chave fornecidos pelo reencaminhamento são comunicação bidirecional, s/mem ao longo dos limites da rede com TCP gosto limitação, deteção de ponto final, estado de conectividade e sobreposta segurança de ponto final. As capacidades de reencaminhamento diferem tecnologias de integração de nível de rede, tais como VPN no que pode ser confinado a um ponto final de única aplicação num único computador, enquanto que a tecnologia VPN é muito mais intrusiva como baseia-se no alterando o ambiente de rede.

Reencaminhamento Azure tem duas funcionalidades:

1. [Ligações de híbrido](#hybrid-connections) - utiliza o Sockets Web padrão abertos, permitindo-cenários múltiplas plataformas

2. [WCF estações de retransmissão](#wcf-relays) - utiliza o Windows Communication Foundation para ativar a chamadas de procedimento remoto

Híbrido ligações e WCF estações de retransmissão ativar uma ligação segura para assests que existam dentro de uma rede de empresa da empresa. Utilização de uma em cima de outra é dependente as suas necessidades detalhadas abaixo:

|                                    | Reencaminhamento WCF | Ligações de híbrido |
| ---------------------------------- |:---------:|:------------------:|
| **WCF**                            |     x     |                    |
| **.NET core**                      |           |         x          |
| **.NET framework**                 |     x     |         x          |
| **JavaScript/NodeJS**              |           |         x          |
| **Java***                          |           |         x          |
| **Baseada em normas protocolo aberto**  |           |         x          |
| **Vários modelos de Programing RPC** |           |         x          |
*<sub>Por disponibilidade geral</sub>

## <a name="hybrid-connections"></a>Ligações de híbrido

Ligações de implementações do Azure reencaminhamento capacidade é uma evolução segura, abrir protocolo das funcionalidades de reencaminhamento existentes que podem ser implementada no qualquer plataforma e em qualquer idioma que tem uma capacidade WebSocket básica, que explicitamente inclui a API WebSocket em comum browsers para a web. Ligações de híbrido é baseado no HTTP e WebSockets.

## <a name="wcf-relays"></a>WCF estações de retransmissão

O reencaminhamento de WCF funciona para o inteiro .NET Framework (adicionados) e para WCF. Iniciar a ligação entre o seu serviço no local e o serviço de reencaminhamento utilizando um conjunto de enlaces reencaminhamento"WCF". Nos bastidores, as associações de reencaminhamento mapear para novos elementos de encadernação de transporte foi concebidos para criar componentes de canal WCF que se integram com Bus serviço na nuvem.

## <a name="service-history"></a>Histórico de serviço

Ligações de híbrido quer antigo, quatros com o nome de funcionalidade "BizTalk serviços" que foi criada com base no reencaminhamento de WCF do Azure Service Bus. A nova funcionalidade de ligações híbrido completa o reencaminhamento de WCF existente e estas capacidades de duas serviço existirão lado a lado no serviço de reencaminhamento para o futuro previsível; partilhar um gateway comuns, mas são contrário implementações diferentes.

Reencaminhamento de WCF é o reencaminhamento de legado perguntar se que muitos clientes já poderão utilizar com os seus modelos programing WCF.

## <a name="next-steps"></a>Passos seguintes:

- [Reencaminhamento perguntas mais frequentes](relay-faq.md)
- [Criar um espaço de nomes](relay-create-namespace-portal.md)
- [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Introdução ao nó](relay-hybrid-connections-node-get-started.md)