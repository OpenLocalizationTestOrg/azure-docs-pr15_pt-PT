<properties 
    pageTitle="Descrição geral de serviço Bus AMQP com Java | Microsoft Azure" 
    description="Saiba como utilizar Java com o avançadas mensagem colocação Protocol (AMQP) 1.0 no Azure." 
    services="service-bus" 
    documentationCenter="java" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>


# <a name="amqp-10-support-in-service-bus"></a>Suporte de AMQP 1.0 no serviço Bus

Serviço em nuvem Azure Service Bus tanto no local a avançadas mensagem colocar em fila Protocol (AMQP) 1.0 de suporte do [Serviço Bus para o Windows Server (serviço Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) . AMQP permite-lhe construir em diferentes plataformas, aplicações de híbrido utilizando um protocolo padrão aberto. Pode construir aplicações utilizando componentes que foram criadas ao utilizar idiomas diferentes e quadros e executados em diferentes sistemas operativos. Todos estes componentes podem ligar ao serviço Bus e forma totalmente integrada trocam empresas estruturadas mensagens eficientemente e em fidelidade completa.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introdução: O que é AMQP 1.0 e por que motivo é importante?

Normalmente, os produtos de software intermédio orientado por mensagem utilizaram protocolos proprietários para a comunicação entre as aplicações de cliente e o corretor. Isto significa que assim que tiver selecionado corretor de mensagens de um determinado fornecedor, tem de utilizar bibliotecas esse fornecedor para ligar as suas aplicações de cliente para esse corretor. Isto resulta num grau de dependência de fornecedor, uma vez que migrar uma aplicação para outro produto requer alterações de código em todas as aplicações ligadas. 

Além disso, a ligar o corretor mensagens a partir de diferentes fornecedores é difícil. Normalmente, isto requer nível da aplicação pontes para mover mensagens de um sistema para outro e para traduzir entre os respetivos formatos de mensagens proprietários. Este é um requisito comum; Por exemplo, quando deve fornecer uma nova interface unificada sistemas distintos ao mais antigos, ou integrar sistemas de TI seguir uma avaliação de aquisições.

A indústria de software é um negócio mover fast; linguagens de programação novas e estruturas de aplicação são introduzidas a um por vezes desconcertantes ritmo. Da mesma forma, os requisitos dos sistemas informáticos evoluem ao longo do tempo e os programadores pretendem tirar partido das funcionalidades de plataforma mais recentes. No entanto, por vezes, o fornecedor de mensagens seleccionado não suporta estas plataformas. Porque são proprietários protocolos de mensagens, não é possível para outras pessoas fornecer bibliotecas para estas novas plataformas. Por isso, tem de utilizar abordagens como construir gateways ou pontes permitem-lhe continuar a utilizar o produto de mensagens.

O desenvolvimento do avançadas mensagem colocação Protocol (AMQP) 1.0 foi de segurança por estes problemas. É originária na JP Machado Chase, quem, como empresas de serviços financeiras mais, são muito carregados utilizadores do software intermédio orientado por mensagem. O objetivo foi simple: para criar um protocolo de mensagens de abrir padrão proporcionou criar aplicações baseadas na mensagem utilizando os componentes construídas utilizando idiomas diferentes, quadros e sistemas operativos, tudo utilizando o melhor da raça componentes a partir de um intervalo de fornecedores.

## <a name="amqp-10-technical-features"></a>Funcionalidades de técnicas AMQP 1.0

AMQP 1.0 é um protocolo mensagens eficiente, fiável, fio nível que pode utilizar para criar robusta, em diferentes plataformas, aplicações de mensagens. O protocolo tem um objetivo simples: Definir mecânica da transferência segura, fiável e eficiente de mensagens entre duas partes. As próprias mensagens são codificadas com uma representação de dados portáteis que permite heterogéneos remetentes e destinatários para mensagens de empresas estruturadas na fidelidade completa do exchange. Segue-se um resumo das funcionalidades mais importantes:

*    **Efficient**: AMQP 1.0 é uma ligação orientados para o protocolo que utiliza uma codificação binário para as instruções de protocolo e as mensagens de empresas transferida sobre a mesma. Incorpora a esquemas sofisticadas de controlo do fluxo para maximizar a utilização da rede e os componentes ligados. Assim sendo, o protocolo foi concebido para obter um equilíbrio entre eficiência, flexibilidade e interoperabilidade.
*    **Fiáveis**: protocolo de AMQP 1.0 permite que as mensagens trocadas com um intervalo de fiabilidade garantias, a partir do fire-e-se esqueça de fiável, exatamente-uma vez confirmada entrega.
*    **Flexível**: AMQP 1.0 é um protocolo flexível que pode ser utilizado para suportar topologias diferentes. O mesmo protocolo pode ser utilizado para comunicações de cliente de cliente, cliente para corretor e corretor para corretor.
*    **Modelo de corretor independente**: especificação de AMQP 1.0 não efetuar quaisquer requisitos no modelo de mensagens utilizado por um corretor. Isto significa que é possível adicionar facilmente AMQP 1.0 suporte a corretor mensagens existente.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 é uma norma (com um capital')

AMQP 1.0 tiver sido em desenvolvimento desde 2008 por um grupo de core de mais de 20 empresas, fornecedores de tecnologia de apoio e empresas do utilizador final. Durante a nesse momento, empresas de utilizador tem Contribuiu com os seus requisitos de empresas reais e os fornecedores de tecnologia tem evoluiu um o protocolo para cumprir essas exigências. Ao longo do processo, fornecedores participaram em seminários na qual colaboraram para validar a possível a interoperabilidade entre os respetivos implementações.

De Outubro de 2011, o trabalho de desenvolvimento que transitaram para um comité técnico dentro da empresa para a progredir de estruturadas informações normas (organização de normalização) e organização de normalização AMQP 1.0 padrão foi disponibilizado em Outubro de 2012. As seguintes empresas participaram na Comissão técnico durante o desenvolvimento do padrão:

*    **Fornecedores de tecnologia**: Axway Software, Huawei tecnologias, IIT Software, INETCO sistemas, Kaazing, Microsoft, Mitre Corporation, Primeton tecnologias, progresso Software, vermelho chapéu, SITA, Software AG, sistemas de Solace, VMware, WSO2, Zenika.
*    **Empresas de utilizador**: banco da América, Suisse de crédito, marcos alemães Boerse, Goldman Sachs, JPMorgan Chase.

Alguns dos benefícios de normas abertas frequentemente determinados incluem:

*    Menos probabilidades de bloqueio no fornecedor
*    Interoperabilidade
*    Vasta disponibilidade de bibliotecas e ferramentas
*    Proteção contra a validade
*    Disponibilidade de docentes com conhecimentos
*    Risco inferior e fácil

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 e Service Bus

Suporte de AMQP 1.0 no Azure Service Bus significa que agora pode tirar partido de colocação de serviço Bus e publicar/subscrever juntamente funcionalidades mensagens a partir de um intervalo de plataformas de utilizando um protocolo binário eficiente. Além disso, pode criar aplicações composto por componentes construídas utilizando uma mistura de idiomas, quadros e sistemas operativos.

A figura seguinte ilustra uma implementação de exemplo na qual os clientes de Java em execução no Linux, escrito utilizando o padrão Java mensagem serviço (JMS) API e os clientes de .NET em execução no Windows, trocam mensagens através do serviço Bus utilizando AMQP 1.0.

![][0]

**Figura 1: Cenário de implementação de exemplo a mostrar em diferentes plataformas mensagens com o serviço Bus e AMQP 1.0**

Neste momento, as seguintes bibliotecas do cliente são conhecidas para funcionar com o serviço Bus:

| Idioma | Biblioteca                                                                       |
|----------|-------------------------------------------------------------------------------|
| Java     | Cliente Apache Qpid Java mensagem serviço (JMS)<br/>Cliente IIT Software SwiftMQ Java |
| C        | Apache Qpid protão-C                                                          |
| PHP      | Apache Qpid protão-PHP                                                        |
| Python   | Apache Qpid protão-Python                                                     |


**Figura 2: Índice AMQP 1.0 bibliotecas do cliente**

Para obter mais informações sobre como obter e utilizar estas bibliotecas com o serviço Bus, consulte o [Guia do Programador de serviço, Bus AMQP][]. Consulte a secção [os passos seguintes](service-bus-java-amqp-overview.md#next-steps) para hiperligações para mais informações.

## <a name="summary"></a>Resumo

*    AMQP 1.0 é um protocolo mensagens aberto e fiável que pode utilizar para criar em diferentes plataformas, aplicações híbridas. AMQP 1.0 é uma norma de organização de normalização.
*    Suporte de AMQP 1.0 está agora disponível no Azure Service Bus, bem como serviço Bus para o Windows Server (serviço Bus 1.1). Preços são o mesmo que para os protocolos existentes.

## <a name="next-steps"></a>Próximos passos

Visite as ligações seguintes para obter mais informações sobre o suporte AMQP no serviço Bus.

*    [Como utilizar AMQP 1.0 com a API do serviço Bus .NET](service-bus-dotnet-advanced-message-queuing.md)
*    [Como utilizar o Java mensagem serviço (JMS) API com o serviço Bus & AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Guia do programador AMQP Bus de serviço][]
*    [Especificação de avançadas mensagem colocação Protocol (AMQP) versão 1.0 de organização de normalização](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)

[0]: ./media/service-bus-java-amqp-overview/Example1.png
[Guia do programador AMQP Bus de serviço]: service-bus-amqp-dotnet.md

 
