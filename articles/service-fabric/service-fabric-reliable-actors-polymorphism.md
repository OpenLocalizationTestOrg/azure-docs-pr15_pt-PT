<properties
   pageTitle="Polimorfismo no quadro intervenientes fiável | Microsoft Azure"
   description="Crie hierarquias de .NET interfaces e de tipos no quadro intervenientes fiável para reutilizar funcionalidades e definições de API."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/07/2016"
   ms.author="seanmck"/>

# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfismo no quadro fiável intervenientes

A arquitetura de intervenientes fiável permite-lhe construir intervenientes com muitas das mesmas técnicas que utilizaria na estrutura orientada para objectos. Uma dessas técnicas é polimorfismo, que permite tipos e interfaces para herdam mais generalized pais. Herança no quadro intervenientes fidedigna segue geralmente o modelo de .NET com algumas restrições adicionais.

## <a name="interfaces"></a>Interfaces

A arquitetura de intervenientes fiável requer que definir pelo menos uma interface para ser implementada pelo seu tipo de ator. Esta interface é utilizada para gerar uma classe de proxy que pode ser utilizada pelo clientes para comunicar com os seus intervenientes. Interfaces podem herdar a partir de outras interfaces desde que cada interface que é implementada através de um tipo de ator e todos os respetivos principais finalmente deriva a IActor. IActor é a interface base definidos pelo plataforma aos intervenientes. Assim, o exemplo polimorfismo clássica utilizando formas poderá ter um aspeto da seguinte forma:

![Hierarquia de interface aos intervenientes de forma][shapes-interface-hierarchy]


## <a name="types"></a>Tipos de

Também pode criar uma hierarquia dos tipos de ator, que derivem da classe de Ator base fornecido pela plataforma. No caso de formas, pode ter uma base `Shape` tipo:

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```

Subtypes de `Shape` pode substituir métodos da base.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```

Nota a `ActorService` atributo o tipo do ator. Este atributo indica a arquitetura do Ator fiável que deverá criar automaticamente um serviço de alojamento intervenientes deste tipo. Em alguns casos, poderá pretender criar um tipo de base que exclusivamente destina-se para partilhar a funcionalidade com subtipos e nunca será utilizado para criar uma instância betão intervenientes. Nestes casos, deve utilizar o `abstract` palavra-chave para indicar que nunca irá criar um ator com base nesse tipo.


## <a name="next-steps"></a>Próximos passos

- Consulte o artigo [como a arquitetura de intervenientes fiável tira partido de plataforma do serviço ferro](service-fabric-reliable-actors-platform.md) para fornecer a fiabilidade, escalabilidade e estado consistente.
- Saiba mais sobre o [ciclo de vida do ator](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
