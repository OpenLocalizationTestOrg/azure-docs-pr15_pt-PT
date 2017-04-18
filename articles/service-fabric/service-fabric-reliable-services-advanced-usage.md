<properties
   pageTitle="Advanced a utilização dos serviços fiável | Microsoft Azure"
   description="Saiba mais sobre a utilização avançada fiável do serviço ferro serviços de para flexibilidade nos seus serviços."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>A utilização dos serviços fiável modelo de programação de avançadas
Azure Service ferro simplifica escrever e gerir serviços fiáveis com e sem a com estado. Este guia irá falar sobre avançadas utilizações dos serviços fiável para obter mais controlo e flexibilidade através dos serviços. Antes de a ler neste guia, familiarizar-se com [Os serviços fiável modelo de programação](service-fabric-reliable-services-introduction.md).

Serviços de estado e sem estado têm dois pontos de entrada principal para o código de utilizador:

 - `RunAsync`é um ponto de entrada uso geral para o código de serviço.
 - `CreateServiceReplicaListeners`e `CreateServiceInstanceListeners` é para abrir listeners de comunicação para pedidos de cliente.
 
Para a maioria dos serviços, estes pontos de duas entrada são suficientes. Em alguns casos raros quando é necessário, uma maior controlo sobre o ciclo de vida de um serviço eventos de ciclo de vida adicionais estão disponíveis.

## <a name="stateless-service-instance-lifecycle"></a>Ciclo de vida do serviço sem estado instância

Ciclo de vida de um serviço sem estado é muito simple. Um serviço sem estado apenas pode ser aberto, fechado ou cancelado. `RunAsync`num serviço sem estado é executado quando uma instância do serviço é aberta e cancelada quando uma instância do serviço é fechada ou cancelada. 

Apesar de `RunAsync` devem ser suficientes em quase todos os casos, o abrir, fechar e eventos de interrupção num serviço sem estado também estão disponíveis:

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
  OnOpenAsync chama-se-á quando a instância do Estado de serviço está prestes a ser utilizado. Serviço adicional inicialização tarefas podem ser iniciadas neste momento.

- `Task OnCloseAsync(CancellationToken)`
  OnCloseAsync denomina-se-á quando a instância do serviço sem estado vai ser correctamente encerrar. Isto pode ocorrer quando está a ser actualizado código do serviço, a instância do serviço está a ser movida devido ao balanceamento de carga ou uma falha de breves é detectada. OnCloseAsync pode ser utilizado para com segurança fechar todos os recursos, pare qualquer processamento em segundo plano, guardar externo Estado de conclusão ou fechar premida ligações existentes.

- `void OnAbort()`
  OnAbort denomina-se-á quando a instância do Estado de serviço está a ser forçada encerrar. Esta opção é geralmente denominada quando é detetado um erro permanente no nó ou quando ferro de serviço não é possível sujeito gerir o ciclo de vida a instância de serviço devido a falhas internos.

## <a name="stateful-service-replica-lifecycle"></a>Ciclo de vida do serviço com estado réplica

Ciclo de vida de uma réplica com estado do serviço é muito mais complexo do que uma instância do Estado de serviço. Além para abrir, fechar e interrupção eventos, uma réplica com estado do serviço sofre alterações funções durante a sua vida útil. Quando uma réplica serviço com estado muda função, o `OnChangeRoleAsync` evento é acionado:

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)`
  OnChangeRoleAsync chama-se-á quando a réplica com estado do serviço está a alterar função, por exemplo para principal ou secundário. Réplicas primárias são atribuídas o estado de escrita (são permitidos para criar e escrever fiável coleções de sites). Réplicas secundárias são dadas estado de leitura (apenas pode ler a partir de existente coleções fiável). A maior parte dos trabalho num serviço de estado é executado na réplica principal. Réplicas secundárias podem executar validação só de leitura, criação de relatórios, extração de dados ou outras tarefas de só de leitura.

No serviço de estado, apenas a réplica principal tenha acesso de escrita para o estado e, por conseguinte, é geralmente quando o serviço está a executar trabalho real. O `RunAsync` método num serviço de estado é executado apenas quando a réplica com estado do serviço é principal. O `RunAsync` método foi cancelado quando função uma réplica primária é alterada afastando-o de principal e durante os eventos de fecho e cancelamento. 

Utilizar o `OnChangeRoleAsync` evento permite-lhe executar o trabalho consoante a função de réplica, assim como resposta a alteração da função.

Um serviço com estado também fornece os eventos do ciclo de vida quatro mesmo como um serviço sem estado, com a mesma semântica e casos de utilização:

- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`



## <a name="next-steps"></a>Próximos passos
Para obter mais avançados Tópicos relacionados com a ferro de serviço, consulte os artigos seguintes:

- [Configurar os serviços de fiável com estado](service-fabric-reliable-services-configuration.md)

- [Introdução de estado de funcionamento do serviço ferro](service-fabric-health-introduction.md)

- [Utilizar relatórios de estado de funcionamento do sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Configurar os serviços com o serviço ferro Cluster Gestor de recursos](service-fabric-cluster-resource-manager-configure-services.md)
