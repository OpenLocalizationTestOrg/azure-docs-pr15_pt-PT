<properties
   pageTitle="Notas intervenientes fiáveis ator escreva serialização | Microsoft Azure"
   description="Fala sobre requisitos básicos para definir classes serializáveis que podem ser utilizadas para definir o serviço ferro fiável intervenientes Estados-membros e interfaces"
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
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Notas no serviço ferro fiável intervenientes escreva serialização


Os argumentos de todos os métodos, tipos de resultados da tarefas devolvidas por cada método de uma interface do ator e objetos armazenados em Gestor de estado de um ator devem ser [Dados contrato serializável](https://msdn.microsoft.com/library/ms731923.aspx)... Também se aplica para os argumentos dos métodos definidos em [interfaces de eventos do ator](service-fabric-reliable-actors-events.md#actor-events). (Métodos de interface de evento ator sempre devolvem void.)

## <a name="custom-data-types"></a>Tipos de dados personalizados

Neste exemplo, a interface do ator seguinte define um método que devolva um tipo de dados personalizados denominado `VoicemailBox`.

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

A interface é impelemented por um ator, que utiliza o Gestor de estado para armazenar uma `VoicemailBox` objeto:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

Neste exemplo, o `VoicemailBox` objeto está serializado quando:
 - O objeto é transmitido entre uma instância do ator e um autor de chamada.
 - O objeto é guardado no Gestor de estado que é mantido disco e replicada para outros nós.
 
A arquitetura do Ator fiável utiliza DataContract serialização. Por isso, os objetos de dados personalizados e os seus membros devem ser anotados com os atributos **DataContract** e **DataMember** , respetivamente

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```

```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```

## <a name="next-steps"></a>Próximos passos
 - [Colecção de ciclo de vida e lixo ator](service-fabric-reliable-actors-lifecycle.md)
 - [Temporizadores ator e lembretes](service-fabric-reliable-actors-timers-reminders.md)
 - [Eventos de ator](service-fabric-reliable-actors-events.md)
 - [Reentrância ator](service-fabric-reliable-actors-reentrancy.md)
 - [Polimorfismo ator e padrões de design orientada para objectos](service-fabric-reliable-actors-polymorphism.md)
 - [Diagnósticos do ator e monitorização de desempenho](service-fabric-reliable-actors-diagnostics.md)
