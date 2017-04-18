<properties
   pageTitle="Testability: Comunicação de serviço | Microsoft Azure"
   description="Comunicação do serviço de serviço é um ponto de crítico integração de uma aplicação de serviço ferro. Este artigo aborda considerações de estrutura e técnicas testes."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-testability-scenarios-service-communication"></a>Cenários do serviço ferro testability: comunicação de serviço

Microservices e superfície de estilos de arquitectura orientados para serviços naturalmente no Azure Service ferro. Nestes tipos de arquitecturas distribuídas, componentized microservice aplicações são normalmente compostos por vários serviços que precisam de falar entre si. No mesmo casos mais simples, geralmente tiver, pelo menos, um serviço web sem estado e um serviço de armazenamento de dados com estado que precisa de comunicar.

Comunicação do serviço de serviço é um ponto de crítico integração de uma aplicação, uma vez que cada serviço expõe uma API remota para outros serviços. Trabalhar com um conjunto de limites de API que envolve e/s geralmente requer algum cuidado, com uma boa quantidade de ensaio e validação.

Existem várias considerações para fazer quando estes limites de serviço são com fios em conjunto num sistema distribuído:

 - *Protocolo de transporte*. Irá utilizar HTTP para maior possível a interoperabilidade ou protocolo binário personalizado para um débito máximo?
 - *Processamento de erros*. Como serão efetuados erros permanentes e breves? O que acontece quando move um serviço para um nó diferente?
 - *Limites de tempo e a latência*. Em aplicações de várias camadas, como irá cada camada de serviço lidar latência através da pilha e para o utilizador?

Se utilizar um dos componentes de comunicação do serviço incorporados fornecidos pelo serviço ferro ou construir a sua própria, as interações entre os seus serviços de testes é fundamental para garantir que RDP na sua aplicação.

## <a name="prepare-for-services-to-move"></a>Preparar para serviços mover

Instâncias do serviço podem deslocar-se ao longo do tempo. Este é especialmente true quando são configurados com carga métricas para balanceamento de recursos ideal obtidas especialmente personalizada. Serviço ferro move as instâncias do serviço para maximizar a sua disponibilidade mesmo durante atualiza os, activações pós-falha, escala de saída e outras situações que ocorrem ao longo do tempo de vida de um sistema distribuído.

Como serviços deslocar-se no cluster, os seus clientes e outros serviços deverão estar preparados para processar dois cenários quando falar com um serviço:

- A instância ou partição de réplica serviço foi movido desde a última vez que talked à mesma. Esta é uma parte de um ciclo de vida do serviço normal e, devem ser esperada deverá acontecer durante a duração da sua aplicação.
- A instância ou partição de réplica serviço está a mover. Apesar de activação pós-falha de um serviço a partir de um nó para outro muito rapidamente ocorre no serviço ferro, poderão existir um atraso na disponibilidade se o componente de comunicação do seu serviço está lento começar.

Processamento estes cenários correctamente é importante para um sistema de execução suave. Para fazê-lo, tenha em conta que:

- Cada serviço que pode ser ligado ao tem um *endereço* de que o mesmo recebe (por exemplo, HTTP ou WebSockets). Quando move o cursor uma instância do serviço ou partição, muda respectivo ponto final de endereço. (Esta passa para um nó diferente com um endereço IP diferente.) Se estiver a utilizar os componentes de comunicação incorporados, irão tratar voltar a resolução de serviço de endereços para si.
- Poderão existir um aumento temporário de latência serviço como o serviço instância é iniciado o respetivo escuta novamente. Isto depende quão rapidamente o serviço é aberta a escuta depois da instância do serviço é movida.
- Qualquer ligações existentes tem de ser fechado e aberto novamente depois do serviço abre num novo nó. Um encerramento nó com êxito ou reinício permite tempo para ligações existentes encerrar correctamente.

### <a name="test-it-move-service-instances"></a>Testá-la: mover instâncias do serviço

Ao utilizar ferramentas de testability do serviço ferro, pode criar um cenário de teste para testar estas situações de diferentes formas:

1. Deslocar-se réplica principal de um serviço de estado.

    A réplica principal de uma partição de serviço com estado pode ser movida para qualquer número das razões. Utilize esta opção para a réplica primária de uma partição específica para ver como os seus serviços de reagirem para a mudança de forma muito controlada de destino.

    ```powershell

    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService

    ```

2. Pare de um nó.

    Quando um nó está parado, o serviço ferro move todas as instâncias do serviço ou a partições que foram nesse nó para um dos outros nós disponíveis no cluster. Utilize esta opção para testar uma situação em que um nó é perdido a partir do seu cluster e todas as ocorrências de serviço e réplicas nesse nó tem de mover.

    Pode deixar de um nó utilizando o cmdlet do PowerShell **Parar ServiceFabricNode** :

    ```powershell

    PS > Restart-ServiceFabricNode -NodeName Node_1

    ```

## <a name="maintain-service-availability"></a>Manter a disponibilidade do serviço

Como uma plataforma ferro serviço foi concebido para fornecer elevada disponibilidade dos seus serviços. Mas em casos extremos, problemas de infraestrutura subjacente ainda podem causar a indisponibilidade. É importante testar a estes cenários, demasiado.

Serviços com estado utilizam um sistema baseado em quórum para criar uma réplica estado para elevada disponibilidade. Isto significa que um quórum de réplicas tem de estar disponível para executar operações de escrita. Em casos raros, tal como uma falha de hardware executarão, um quórum de réplicas poderá não estar disponível. Nestes casos, não conseguir executar operações de escrita, mas ainda conseguir executar operações de leitura.

### <a name="test-it-write-operation-unavailability"></a>Testá-la: escrita indisponibilidade de operação

Ao utilizar as ferramentas de testability na ferro de serviço, pode inserir uma falha de originar perda quórum como um teste. Apesar de é raro essa um cenário, é importante que os clientes e serviços que dependem de um serviço com estado são preparados para lidar com situações onde não é possível fazem escrever pedidos ao mesmo. Também é importante que o próprio serviço com estado é conhecimento desta possibilidade e correctamente a pode comunicar para autores de chamadas.

Pode induzir perda quórum utilizando o cmdlet do PowerShell **Invocar ServiceFabricPartitionQuorumLoss** :

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

Neste exemplo, definir `QuorumLossMode` para `QuorumReplicas` para indicar que pretendemos induzir perda quórum sem ser para baixo de todas as réplicas. Desta forma, operações de leitura são continua a ser possíveis. Para testar um cenário de onde estiver disponível uma partição inteira, pode configurar este parâmetro para `AllReplicas`.

## <a name="next-steps"></a>Próximos passos

[Saiba mais sobre ações testability](service-fabric-testability-actions.md)

[Saiba mais sobre cenários testability](service-fabric-testability-scenarios.md)
