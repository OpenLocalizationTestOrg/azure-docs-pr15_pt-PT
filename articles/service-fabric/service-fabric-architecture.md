<properties
   pageTitle="Arquitetura de serviço ferro | Microsoft Azure"
   description="Serviço ferro é uma plataforma de sistemas distribuídos utilizada para criar aplicações dimensionáveis, fiáveis e geridos facilmente para a nuvem. Este artigo mostra a arquitetura do serviço ferro."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor="rishirsinha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/09/2016"
   ms.author="rsinha"/>

# <a name="service-fabric-architecture"></a>Arquitetura de ferro de serviço

Serviço ferro é criado com subsistemas camados. Estes subsistemas permitem-lhe escrever aplicações que são:

* Altamente disponíveis
* Dimensionáveis
* Fácil
* Testable

O diagrama seguinte mostra os subsistemas principais do serviço ferro.

![Diagrama de arquitectura ferro de serviço](media/service-fabric-architecture/service-fabric-architecture.png)

Num sistema distribuído, a capacidade de comunicar entre nós num cluster de forma segura é crucial. Na base da pilha é o subsystem de transporte, que fornece comunicação segura entre nós. Acima de transporte subsystem se situar subsystem a Federação, que clusters os nós diferentes para uma única entidade (denominada clusters) para que possa detectar falhas, executar eleições políticas de caráter de preenchimento e fornecer o encaminhamento de consistentes ferro de serviço. Subsystem a fiabilidade, colocado na parte superior de subsystem Federação, é responsável por fiabilidade dos serviços de serviço ferro através de mecanismos como replicação, gestão de recursos e activação pós-falha. O subsystem Federação também subjacente ao subsystem alojamento e ativação, que gere o ciclo de vida de uma aplicação num único nó. O subsystem gestão gere o ciclo de vida de aplicações e serviços. O subsystem testability ajuda os programadores de aplicações testar dos seus serviços através de falhas simuladas antes e depois implementá-lo na aplicações e serviços ambientes de produção. Serviço ferro fornece a capacidade de resolver localizações de serviço através do seu subsystem de comunicação. Os modelos de programação de aplicação expostos para os programadores colocados na parte superior desses subsistemas juntamente com o modelo de aplicação para ativar ferramentas.

## <a name="transport-subsystem"></a>Subsystem de transporte
O subsystem de transporte implementa um canal de comunicação ponto a ponto datagrama. Este canal é utilizado para comunicação dentro clusters de ferro de serviço e a comunicação entre o serviço de cluster ferro e clientes. Suporta unidirecional e padrões de comunicação de resposta do pedido, que fornece a base de execução de difusão e multicast na camada de Federação. O subsystem de transporte protege comunicação utilizando X509 certificados ou de segurança do Windows. Este subsystem é utilizado pelo serviço ferro e não está acessível diretamente para os programadores de programação de aplicações.

## <a name="federation-subsystem"></a>Subsystem Federação
Para poder motivo sobre um conjunto de nós num sistema distribuído, tem de ter uma vista consistente do sistema. O subsystem Federação utiliza as primitivas de comunicação fornecidas pelo subsystem transporte e stitches os vários nós num único cluster unificada-pode motivo sobre. Fornece as primitivas sistemas distribuídos necessárias aos outros subsistemas - detecção de falha de eleições políticas de caráter de preenchimento e encaminhamento consistentes. O subsystem Federação baseia-se na parte superior de tabelas de hash distribuído com um espaço token de 128-bit. O subsystem cria uma topologia em anel sobre os nós, com cada nó tocar a ser atribuída um subconjunto do token espaço para a propriedade. Para deteção falha, a camada utiliza um mecanismo locação com base em coração beating e arbitragem. Também garante a subsystem Federação através de associação complexa e protocolos de partida que existe um único proprietário de um token em qualquer altura. Isto fornecer eleições políticas de caráter de preenchimento e garantias de encaminhamento consistentes.

## <a name="reliability-subsystem"></a>Subsystem fiabilidade
O subsystem fiabilidade fornece o mecanismos para disponibilizar o estado de um serviço de serviço ferro altamente através da utilização de _replicação_, _Gestor de activação pós-falha_e _Balanceador de recursos_.

* A replicação assegura que as alterações de estado na réplica serviço principal automaticamente vai ser replicadas para réplicas secundárias, manter a consistência entre as principais e secundárias réplicas num conjunto de réplica do serviço. A replicação é responsável pela gestão de quórum entre as réplicas do conjunto de réplicas. Interage com a unidade de activação pós-falha para obter a lista de operações para criar uma réplica e o agente de reconfiguração fornece-lo com a configuração do conjunto de réplica. Que a configuração indica que réplicas as operações precisam de ser replicadas. Serviço ferro fornece uma replicação predefinido denominada ferro replicação, que podem ser utilizadas pela programação API do modelo para disponibilizar o estado do serviço altamente e fiável.
* O Gestor de activação pós-falha assegura que quando nós são adicionados ou removidos cluster, a carga é automaticamente redistribuída em todos os nós disponíveis. Se um nó no cluster falhar, cluster serão automaticamente reconfigurar as réplicas do serviço para manter a disponibilidade.
* O Gestor de recursos coloca réplicas do serviço através de domínio de falha do cluster e assegura que todas as unidades de activação pós-falha são operacionais. O Gestor de recursos também saldos de recursos do serviço ao longo do conjunto de partilhada subjacente de nós de cluster para alcançar a distribuição de carga uniforme ideal.

## <a name="management-subsystem"></a>Subsystem de gestão
O subsystem gestão fornece serviço de ponto a ponto e gestão de ciclo de vida de aplicações. APIs administrativos e os cmdlets do PowerShell permitem-lhe aprovisionar, implementar, correcção, atualizar e anular aprovisionar aplicações sem perda de disponibilidade. O subsystem gestão executa este através dos seguintes serviços.

* **Gestor de clusters**: Este é o serviço principal que interage com o activação pós-falha Gestor de fiabilidade para colocar as aplicações em nós com base em restrições de colocação de serviço. O Gestor de recursos no activação pós-falha subsystem assegura que os constrangimentos nunca são quebrados. O Gestor de cluster gere o ciclo de vida das aplicações a partir aprovisionar aprovisionamento de anular. Integra-se com o Gestor de estado de funcionamento para se certificar de que a disponibilidade de aplicação não é perdida através da perspetiva semântico do Estado de funcionamento da durante as actualizações.
* **Gestor de estado de funcionamento**: este serviço permite a monitorização de estado de funcionamento de aplicações, serviços e entidades de cluster. Entidades de cluster (como nós, a partições de serviço e réplicas) podem comunicar informações do Estado de funcionamento, que são agregadas, em seguida, para o arquivo de estado de funcionamento centralizada. Estas informações de estado de funcionamento fornecem um instantâneo global ponto-in-time estado de funcionamento de serviços e nós distribuídos ao longo de vários nós num cluster, permitindo-lhe efetuar quaisquer ações de lentes conforme seja necessárias. Consulta de estado de funcionamento que APIs permitem-lhe os eventos de estado de funcionamento da consulta reportados subsystem o estado de funcionamento. A consulta de estado de funcionamento APIs devolvem os dados de estado de funcionamento observou armazenados no arquivo de estado de funcionamento ou agregado, interpretada dados de estado de funcionamento de uma entidade de cluster específico.
* **Imagem da loja**: este serviço fornece armazenamento e distribuição dos binários de aplicação. Este serviço fornece um arquivo de ficheiros distribuído simples onde as aplicações são carregadas para e transferidas a partir de.


## <a name="hosting-subsystem"></a>Subsystem alojamento
O Gestor de cluster informa o alojamento subsystem (a ser executado em cada nó) os serviços que necessita para gerir para um determinado nó. O alojamento subsystem, em seguida, gere o ciclo de vida da aplicação nesse nó. O interage com os componentes de fiabilidade e o estado de funcionamento para garantir que as réplicas são colocadas corretamente e que estão em bom estadas.

## <a name="communication-subsystem"></a>Subsystem de comunicação
Este subsystem fornece mensagens fiável dentro de deteção de cluster e serviço através do serviço de atribuição de nomes. O serviço de nomenclatura resolve nomes de serviço para uma localização no cluster e permite aos utilizadores gerir propriedades e nomes de serviço. Utilizar o serviço de atribuição de nomes, os clientes em segurança podem comunicar com qualquer nó cluster para resolver um nome de serviço e obter metadados de serviço. Utilizar um cliente de nomenclatura API simple, os utilizadores do serviço ferro podem desenvolver serviços e clientes com capacidade para resolver a localização de rede atual apesar dinamismo nó ou voltar de dimensionamento do cluster.

## <a name="testability-subsystem"></a>Testability subsystem
Testability é um conjunto de ferramentas concebidos especificamente para serviços criada com base em ferro de serviço de teste. As ferramentas de permitir que um programador induzir falhas significativas e executar cenários de teste para exercer e validar dos vários Estados e transições um serviço irá deparar em toda a sua vida útil, todas de uma forma controlada e segura facilmente. Testability também fornece um mecanismos para executar testes mais longo do que podem fazer iteração através de vários falhas possíveis sem perder disponibilidade. Isto fornece um ambiente de teste de produção.
