<properties
   pageTitle="Diagnósticos de serviços fiáveis com estado | Microsoft Azure"
   description="Funcionalidade de diagnóstico para serviços fiáveis com estado"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/17/2016"
   ms.author="alanwar"/>

# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funcionalidade de diagnóstico para serviços fiáveis com estado
A classe de com estado fiável serviços StatefulServiceBase emite [Origemdoevento](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eventos que podem ser utilizados para o serviço de depuração, fornecer informações para o modo como o tempo de execução está a funcionar e ajuda para resolução de problemas.

## <a name="eventsource-events"></a>Origemdoevento eventos
O nome de Origemdoevento para a classe de com estado fiável serviços StatefulServiceBase é "Serviços da ServiceFabric Microsoft". Eventos de origem este evento aparecem na janela de [Eventos de diagnósticos](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando o serviço está a ser [depurado no Visual Studio](service-fabric-debugging-your-application.md).

Exemplos de ferramentas e tecnologias que ajudam a recolher e/ou visualizar os eventos OrigemEvento são [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnósticos do Microsoft Azure](../cloud-services/cloud-services-dotnet-diagnostics.md)e a [Biblioteca do Microsoft TraceEvent](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Eventos

|Nome do evento|ID do evento|Nível|Descrição do evento|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|Informativos|Emitida quando tarefa do serviço de RunAsync for iniciada|
|StatefulRunAsyncCancellation|2|Informativos|Emitida quando tarefa do serviço de RunAsync foi cancelada|
|StatefulRunAsyncCompletion|3|Informativos|Emitida quando serviço RunAsync tarefa for concluída|
|StatefulRunAsyncSlowCancellation|4|Aviso|Emitida quando a tarefa de RunAsync serviço demora demasiado tempo para concluir o cancelamento|
|StatefulRunAsyncFailure|5|Erro|Emitida quando tarefa do serviço de RunAsync inicia uma exceção|

## <a name="interpret-events"></a>Interpretar eventos

Eventos StatefulRunAsyncInvocation, StatefulRunAsyncCompletion e StatefulRunAsyncCancellation são úteis para o gravador de serviço para compreender o ciclo de vida de um serviço –, bem como a hora de quando um serviço é iniciado, cancelado ou concluído. Isto pode ser útil quando depurar problemas com o serviço ou Noções sobre o ciclo de vida do serviço.

Escritores do serviço de devem prestar atenção fechar StatefulRunAsyncSlowCancellation e StatefulRunAsyncFailure eventos de uma vez que indicam problemas com o serviço.

StatefulRunAsyncFailure é emitido sempre que a tarefa de RunAsync() serviço inicia uma exceção. Normalmente, uma exceção iniciada indica um erro ou erros no serviço. Para além disso, a exceção faz com que o serviço falham, para que este é movido para um nó diferente. Isto pode ser uma operação dispendiosa e pode atrasar pedidos recebidos enquanto o serviço é movido. Escritores do serviço de devem determinar a causa da exceção e, se possível, mitigá-lo.

StatefulRunAsyncSlowCancellation é emitido sempre que um pedido de cancelamento para a tarefa de RunAsync demora mais de quatro segundos. Quando um serviço demora demasiado tempo a concluir cancelamento, o seu impacto, a capacidade para o serviço de ser reiniciado rapidamente noutro nó. Isto pode afetar a disponibilidade geral do serviço.
