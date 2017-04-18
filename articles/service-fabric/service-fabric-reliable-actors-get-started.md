<properties
   pageTitle="Introdução ao serviço ferro fiável intervenientes | Microsoft Azure"
   description="Neste tutorial orienta-o através dos passos de criar, depuração e implementar um serviço baseado em ator simple utilizando o serviço ferro fiável intervenientes."
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
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="getting-started-with-reliable-actors"></a>Introdução ao fiável intervenientes

> [AZURE.SELECTOR]
- [C# no Windows](service-fabric-reliable-actors-get-started.md)
- [Java no Linux](service-fabric-reliable-actors-get-started-java.md)

Este artigo explica as noções básicas dos Azure Service ferro fiável intervenientes e orienta-o através de criar, depuração e implementar uma aplicação do Ator fiável simple no Visual Studio.

## <a name="installation-and-setup"></a>Instalação e configuração
Antes de começar, certifique-se de que tem o ambiente de desenvolvimento do serviço ferro configurar no seu computador.
Se precisar de configurá-lo, consulte o artigo obter instruções detalhadas sobre [como configurar o ambiente de desenvolvimento](service-fabric-get-started.md).

## <a name="basic-concepts"></a>Conceitos básicos
Para começar com intervenientes fiável, apenas terá compreender alguns conceitos básicos:

 * **Serviço do ator**. Intervenientes fiáveis são empacotados no fiável serviços que pode ser implementada no infraestrutura do serviço ferro. Instâncias do ator são ativadas numa instância do serviço com nome.
 
 * **Registo do ator**. Como com os serviços fiável, um serviço de Ator fiável tem de estar registado junto do tempo de execução do serviço ferro. Além disso, o tipo do ator tem de estar registado junto do tempo de execução do Ator.
 
 * **Interface do ator**. A interface do ator é utilizada para definir uma interface pública vivamente escrita de um ator. Na terminologia de modelo do Ator fiável, a interface do ator define os tipos de mensagens que possa compreender o ator e processo. A interface do ator é utilizada por outros intervenientes e aplicações de cliente para "Enviar" (modo assíncrono) mensagens para o ator. Intervenientes fiáveis podem implementar várias interfaces.
 
 * **ActorProxy escolares**. A classe de ActorProxy é utilizada por aplicações de cliente para invocar dos métodos expostos através da interface do ator. A classe de ActorProxy fornece dois funcionalidades importantes:
    * Resolução de nomes: é possível localizar o ator no cluster (localizar o nó do cluster onde está alojado).
    * Processamento de falha:-pode repetir exe método e resolver voltar a localização do ator após, por exemplo, uma falha de que necessita do ator para deslocado para outro nó no cluster.

São as seguintes regras que pertencem aos interfaces do ator mencionar:

- Não podem ser sobrecarregados métodos de interface do ator.
- Interface do ator métodos não devem ter saída, ref ou parâmetros opcionais.
- Interfaces genéricas não são suportadas.

## <a name="create-a-new-project-in-visual-studio"></a>Criar um novo projeto no Visual Studio
Depois de ter instalado as ferramentas de serviço ferro para Visual Studio, pode criar novos tipos de projeto. Os novos tipos de projeto estão na categoria de **nuvem** da caixa de diálogo **Novo projeto** .


![Ferramentas de serviço ferro para Visual Studio - novo projeto][1]

Na caixa de diálogo seguinte, pode escolher o tipo de projeto que pretende criar.

![Modelos de projecto ferro de serviço][5]

Para o projeto HelloWorld, vamos utilizar o serviço do serviço ferro fiável intervenientes.

Depois de ter criado a solução, deverá ver a seguinte estrutura:

![Estrutura de ferro de serviço do project][2]

## <a name="reliable-actors-basic-building-blocks"></a>Fiáveis intervenientes básicas blocos modulares

Uma solução intervenientes fiável típica é composto por três projetos:

* **O projeto de aplicação (MyActorApplication)**. Este é o projeto que pacotes de todos os serviços em conjunto para implementação. Contém os scripts *ApplicationManifest.xml* e PowerShell para gerir a aplicação.

* **O projeto de interface (MyActor.Interfaces)**. Este é o projeto que contém a definição de interface para o ator. No MyActor.Interfaces project, pode definir as interfaces que serão utilizadas pelos intervenientes da solução. Podem ser definidas interfaces do ator qualquer projeto com qualquer nome, no entanto, a interface define o contrato de ator que é partilhado através da aplicação do ator e os clientes de que entra em contacto o ator, por isso, normalmente faz sentido defini-la numa assemblagem que está separado da aplicação do ator e pode ser partilhada por vários outros projetos.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **O projeto de serviço do ator (MyActor)**. Este é o projeto utilizado para definir o serviço do serviço ferro que vai para alojar o ator. A execução da ator nela contidos. Uma implementação do ator é uma classe que deriva do tipo de base `Actor` e implementa interface (s) que é definidos no projeto MyActor.Interfaces. Também tem de uma classe ator implementar um construtor que aceita uma `ActorService` instância e um `ActorId` e transmite-os para a base `Actor` escolares. Isto permite para introdução de dependência construtor de dependências de plataforma.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

O serviço do ator tem de estar registado junto de um tipo de serviço no runtime do serviço ferro. Ordem no serviço do Ator executar as instâncias do ator, o seu tipo de ator também deve estar registado com o serviço do Ator. O `ActorRuntime` método de registo executa este trabalho aos intervenientes.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Se iniciar a partir de um novo projeto no Visual Studio e tiver apenas uma definição de ator, o registo é incluído por predefinição no código de que o Visual Studio gera. Se definir outros intervenientes no serviço, terá de adicionar o registo do ator utilizando:

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [AZURE.TIP] O tempo de execução do serviço ferro intervenientes emite algumas [eventos e contadores de desempenho relacionados com a métodos ator](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). São úteis de diagnóstico e monitorização de desempenho.


## <a name="debugging"></a>Depuração

As ferramentas de serviço ferro para Visual Studio suportam a depuração no seu computador local. Pode iniciar uma sessão de depuração ao atingir a tecla F5. Visual Studio constrói (se necessário) pacotes. Também ser implementada a aplicação no cluster serviço ferro local e anexa o depurador.

Durante o processo de implementação, pode ver o progresso na janela de **saída** .

![Janela de saída depuração ferro de serviço][3]


## <a name="next-steps"></a>Próximos passos
 - [Como intervenientes fiável utilizar a plataforma ferro de serviço](service-fabric-reliable-actors-platform.md)
 - [Gestão de estado do ator](service-fabric-reliable-actors-state-management.md)
 - [Colecção de ciclo de vida e lixo ator](service-fabric-reliable-actors-lifecycle.md)
 - [Documentação de referência do ator API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Código de exemplo](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
