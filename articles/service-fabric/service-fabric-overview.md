<properties
   pageTitle="Descrição geral do serviço ferro | Microsoft Azure"
   description="Uma descrição geral do serviço ferro, onde aplicações são compostos por muitos microservices para fornecer escala e resistência. Serviço ferro é uma plataforma sistemas distribuídos utilizado para construir dimensionáveis, fiáveis e aplicações para o cloud facilmente geridas"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/22/2016"
   ms.author="mfussell"/>

# <a name="overview-of-service-fabric"></a>Descrição geral de ferro de serviço
Serviço ferro é uma plataforma de sistemas distribuídos que torna mais fácil compactar, implementar e gerir microservices e dimensionáveis. Serviço ferro também resolve os desafios significativos no desenvolvimento e gestão de aplicações na nuvem. Os programadores e administradores podem evitar a resolução de problemas de infraestrutura complexa e foco em vez disso, sobre como implementar das cargas de trabalho críticos, excessiva sabendo que estão dimensionáveis, fiável e fácil. Serviço ferro representa a plataforma de software próxima geração intermédio para criar e gerir estas aplicações de escala nuvem de camada 1, classe empresarial.

Este [breve vídeo](https://aka.ms/servicefabricvideo) fornece uma introdução ao serviço ferro e microservices.


## <a name="applications-composed-of-microservices"></a>Aplicações composto por microservices
Serviço ferro permite-lhe criar e gerir aplicações e dimensionáveis composto por microservices em execução densidade muito alta num conjunto partilhado de máquinas (designado por um cluster). Fornece um tempo de execução sofisticado para construir distribuído, dimensionáveis microservices com e sem a com estado. Também fornece as capacidades da gestão de aplicação abrangente de aprovisionamento, implementar, monitorização, a aplicação de patches e atualizar e eliminar implementado aplicações.

Por que motivo é importante uma abordagem de microservices? Sejam as duas principais razões:

1. Permitem-lhe dimensionar diferentes partes da sua aplicação dependendo das suas necessidades.

2. As equipas de programação podem ser mais ágil no gradual saída alterações e assim fornecem funcionalidades aos seus clientes mais rapidamente e com mais frequência.

Serviço ferro oferece muitos Microsoft serviços hoje em dia, incluindo a base de dados do SQL Azure, Azure DocumentDB, Cortana, do Power BI, Microsoft Intune, Azure evento concentradores, Azure IoT, Skype para empresas e muitas core Azure services.

Serviço ferro é adaptadas para a criação de nuvem serviços nativos que podem iniciar pequeno, conforme necessário e fazer crescer para dimensionar grandes com centenas ou milhares de máquinas.

Serviços de Internet escala de hoje foram criados de microservices. Exemplos de microservices incluem gateways protocolo, perfis de utilizador, compras carros de inventário de processamento, filas e coloca em cache. Serviço ferro é uma plataforma de microservices que lhe dá cada microservice um nome exclusivo que pode ser sem estado ou com estado.

Serviço ferro fornece abrangentes runtime e ciclo de vida as capacidades da gestão das aplicações do composto por estas microservices. -Aloja microservices dentro contentores implementado e ativado ao longo do cluster de serviço ferro. Mover a partir do VMs para possíveis de torna contentores um aumento de encomenda de magnitude densidade. Da mesma forma, outro magnitude de ordem no densidade torna-se possíveis movendo a partir de contentores microservices. Por exemplo, num único cluster de base de dados do Azure SQL compreende centenas de máquinas executar dezenas de milhares de contentores alojamento um total de centenas de milhares de bases de dados. Cada base de dados é uma microservice com estado do serviço ferro. O mesmo é verdadeiro relativamente a outros serviços mencionados anteriormente, que é a razão pela qual o termo "hyperscale" é utilizado para descrever as capacidades de serviço ferro. Se contentores dar-lhe alta densidade, em seguida, microservices dar-lhe hyperscale.

Para saber mais sobre a abordagem microservices, leia o artigo [razão pela qual uma abordagem de microservices à criação de aplicações?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Implementação de contentor e orchestration
Serviço ferro é uma [orchestrator](service-fabric-cluster-resource-manager-introduction.md) de microservices através de um cluster de máquinas. Podem ser desenvolvidos Microservices de diversas formas de utilizar os [modelos de programação do serviço ferro](service-fabric-choose-framework.md) a implementar o [Convidado executáveis](service-fabric-deploy-existing-app.md). Serviço ferro pode implementar os serviços de imagens de contentor e nenhumas pode misturar serviços no processos e dos serviços em contentores em conjunto na mesma aplicação. Se pretende apenas [Implementar e gerir contentor imagens](service-fabric-containers-overview.md) através de um cluster de máquinas, o serviço ferro é uma escolha perfeita para este.


## <a name="create-service-fabric-clusters-anywhere"></a>Criar clusters de serviço ferro em qualquer lugar
Pode criar clusters de serviço ferro em vários ambientes, incluindo Azure ou no local, no Windows Server ou no Linux. Além disso, o ambiente de desenvolvimento no SDK é idêntico ao ambiente de produção com sem emuladores envolvidos. Por outras palavras, se for executada no seu cluster de desenvolvimento local-implementa o mesmo cluster noutros ambientes.

Para mais informações sobre como criar clusters no local, leia a [criar um cluster no Windows Server ou Linux](service-fabric-deploy-anywhere.md) ou para Azure criando um cluster [através do portal do Azure](service-fabric-cluster-creation-via-portal.md).

![Plataforma ferro de serviço][Image1]

## <a name="stateless-and-stateful-service-fabric-microservices"></a>Com e sem a com estado microservices ferro de serviço

Serviço ferro permite-lhe criar aplicações que consiste microservices. Sem estado microservices (gateways protocolo, web proxies, etc.) não pretende manter um Estado mutáveis fora qualquer pedido determinado e a sua resposta de serviço. Funções de trabalho do Azure serviços em nuvem são um exemplo de um serviço sem estado. Microservices com estado (contas de utilizador, bases de dados, dispositivos, compras carrinhos, filas, etc.) manter um Estado de autoritativos mutáveis além do pedido e a sua resposta. De uma combinação de microservices com e sem a com estado consistir em aplicações de Internet escala de hoje.

Porque tenho microservices com estado juntamente com os sem estado? Sejam as duas principais razões:

1. A capacidade de criar débito alta, baixa latência, tolerância a falhas falha online de processamento de transações serviços (OLTP) mantendo código e fechar dados no mesmo computador. Alguns exemplos são interativas montras, pesquisa, sistemas de Internet coisas (IoT), sistemas de negociação, sistemas de deteção de processamento e fraude do cartão de crédito e gestão de registos pessoal.

2. Simplificação de estrutura da aplicação. Microservices com estado remover a necessidade de filas adicionais e coloca em cache, tradicionais necessárias para resolver os requisitos de disponibilidade e a latência de uma aplicação puramente sem estado. Serviços com estado são naturalmente alta disponibilidade e a latência baixa, reduzindo o número de mover peças para gerir a sua aplicação como um todo.

Para mais informações sobre os padrões de aplicação com ferro de serviço, leia [cenários de aplicações](service-fabric-application-scenarios.md) e [escolhendo um quadro de modelo de programação](service-fabric-choose-framework.md) do seu serviço

## <a name="application-lifecycle-management"></a>Gestão de ciclo de vida de aplicações
Serviço ferro fornece suporte primeira classe para a gestão de ciclo de vida (ALM) aplicação completo de aplicações na nuvem – a partir do desenvolvimento através de implementação, gestão diária e manutenção para desativar o eventual.

Administradores da aplicação de ativar as capacidades de serviço ferro ALM operadores IT utilizar fluxos de trabalho simples, baixa tátil aprovisionar, implementar, patches, e monitorizar aplicações. Estes fluxos de trabalho incorporados reduzir substancialmente o encargo operadores IT para manter as aplicações disponíveis continuamente.

A maioria das aplicações consistir uma combinação de microservices com e sem a com estado e outros executáveis/runtimes implementado em conjunto. Ao ter tipos fortes em aplicações e microservices embalado, serviço ferro permite a implementação de várias instâncias da aplicação. Cada instância é gerida e atualizada de forma independente. Importante, serviço ferro é possível implementar *qualquer* executáveis ou runtime e torná-las fiável. Por exemplo, serviço ferro implementa ASP.NET Core 1, Node.js, Java VMs, scripts ou mais nada que constituem a sua aplicação.

Para mais informações sobre a gestão de ciclo de vida de aplicação, leia o [ciclo de vida de aplicação](service-fabric-application-lifecycle.md) e sobre como implementar qualquer código consulte [Implementar convidado executável](service-fabric-deploy-existing-app.md)

## <a name="key-capabilities"></a>Capacidades de chaves
Ao utilizar o serviço ferro, pode:

- Desenvolva aplicações previstos excederem consideravelmente dimensionáveis que estão personalizada reparação.

- Desenvolva aplicações composto por microservices utilizando o modelo de programação do serviço ferro. Ou, basta anfitrião convidado executáveis e outros quadros aplicação da sua escolha, tal como ASP.NET Core 1 ou Node.js.

- Desenvolva altamente fiáveis microservices com e sem a com estado.

- Implementar e orquestrar contentores incluem contentores do Windows e contentores Docker ao longo de um cluster. Estes contentores podem contentor convidado executáveis ou fiável microservices com e sem a com estado.  Em ambos os casos, obter porta de contentor para mapeamento de portas de anfitrião, descoberta contentor e automatizado activação pós-falha.

- Simplificar a estrutura da sua aplicação utilizando com estado microservices em vez de caches e filas.

- Implemente para Azure ou para nuvens no local com o Windows Server ou Linux com zero alterações de código. Escrever uma vez e, em seguida, implementar em qualquer lugar para qualquer cluster de hardware de serviço.

- Desenvolva com uma abordagem de "Centro de dados no seu computador". O ambiente de desenvolvimento local é o mesmo código é executado no Azure os centros de dados.

- Implemente aplicações em segundos.

- Implemente aplicações densidade mais elevados que máquinas virtuais, implementar centenas ou milhares de aplicações por máquina.

- Implemente diferentes versões da mesma aplicação lado a lado, pode ser actualizado através de cada forma independente.

- Gerir o ciclo de vida das suas aplicações com estado sem qualquer tempo de inatividade, incluindo actualizações à ruptura e não separáveis.

- Gerir aplicações utilizando .NET APIs, Java (Linux), PowerShell, Azure clip (Linux) ou resto interfaces.

- Atualizar e patches microservices dentro das aplicações de forma independente.

- Monitorizar e diagnosticar o estado de funcionamento das suas aplicações e definir políticas para efetuar reparações automáticas.

- Capacidade de escala de saída ou escala-in o número de nós num cluster, bem como escala de segurança ou escala descendente o tamanho de cada nó, sabendo que as aplicações dimensionar e automaticamente são distribuídas de acordo com os recursos disponíveis.

- Ver o personalizada reparação Balanceador de recurso orquestrar a redistribuição de aplicações ao longo do cluster. Serviço ferro recupera de falhas e optimiza a distribuição de carga com base em recursos disponíveis.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos

* Para obter mais informações:
    * [Por que motivo um microservices abordagem à criação de aplicações?](service-fabric-overview-microservices.md)
    * [Descrição geral de terminologia](service-fabric-technical-overview.md)
* Configurar o seu [ambiente de desenvolvimento](service-fabric-get-started.md) do serviço ferro  
* [Escolher um quadro de modelo de programação](service-fabric-choose-framework.md) do seu serviço

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
