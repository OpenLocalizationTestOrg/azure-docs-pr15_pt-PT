<properties
   pageTitle="Eventos de intervenientes fiáveis | Microsoft Azure"
   description="Introdução à eventos de serviço ferro fiável intervenientes."
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
   ms.date="08/30/2016"
   ms.author="amanbha"/>


# <a name="actor-events"></a>Eventos de ator
Eventos de ator fornecem uma forma para enviar melhor esforço notificações a partir de ator para os clientes. Eventos de ator destinam-se para a comunicação ator para cliente e não devem ser utilizados para a comunicação ator para ator.

Os seguinte fragmentos de código mostram como utilizar eventos do ator na sua aplicação.

Defina uma interface que descreva os eventos publicados pelo ator. Esta interface deve ser provenientes de `IActorEvents` interface. Os argumentos dos métodos tem de ser [contrato serializável de dados](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Os métodos tem de devolver void, como evento de notificações de uma forma e o melhor esforço.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

Declare os eventos publicados pelo ator na interface do ator.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```

No lado do cliente, implemente o processador de eventos.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

No cliente, crie um proxy para o ator que publica o evento e subscrever eventos de respetivos.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

Trabalho activações pós-falha, o ator poderá falhar ao longo da um processo diferente ou o nó. O proxy do ator gere as subscrições ativas e automaticamente subscreve-los novamente. Pode controlar o intervalo de nova subscrição através de `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API. Para anular a subscrição, utilize o `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

No ator, basta publica os eventos como tempo real. Se existirem subscritores do evento, o runtime intervenientes irá enviá-los a notificação.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```

## <a name="next-steps"></a>Próximos passos
 - [Reentrância ator](service-fabric-reliable-actors-reentrancy.md)
 - [Diagnósticos do ator e monitorização de desempenho](service-fabric-reliable-actors-diagnostics.md)
 - [Documentação de referência do ator API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Código de exemplo](https://github.com/Azure/servicefabric-samples)
