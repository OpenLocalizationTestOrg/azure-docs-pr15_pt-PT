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
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="getting-started-with-reliable-actors"></a>Introdução ao fiável intervenientes

> [AZURE.SELECTOR]
- [C# no Windows](service-fabric-reliable-actors-get-started.md)
- [Java no Linux](service-fabric-reliable-actors-get-started-java.md)

Este artigo explica as noções básicas dos Azure Service ferro fiável intervenientes e orienta-o a criar e implementar uma aplicação do Ator fiável simple em Java.

## <a name="installation-and-setup"></a>Instalação e configuração
Antes de começar, certifique-se de que tem o ambiente de desenvolvimento do serviço ferro configurar no seu computador.
Se precisar de configurá-lo, aceda a [introdução no Mac](service-fabric-get-started-mac.md) ou [Linux começar a trabalhar](service-fabric-get-started-linux.md).

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

## <a name="create-an-actor-service"></a>Criar um serviço do ator
Comece por criar uma nova aplicação de serviço ferro. O SDK do ferro de serviço para Linux inclui um Yeoman gerador para fornecer andaime para uma aplicação de serviço ferro com um serviço de estado. Inicie executando Yeoman o seguinte comando:

```bash
$ yo azuresfjava
```

Siga as instruções para criar um **Serviço do Ator fiável**. Para este tutorial, o nome da aplicação "HelloWorldActorApplication" e o ator "HelloWorldActor." Será criado andaime seguinte:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="reliable-actors-basic-building-blocks"></a>Fiáveis intervenientes básicas blocos modulares

Os conceitos básicos descritos anteriormente traduzem para básicos blocos modulares de um serviço de Ator fiável.

### <a name="actor-interface"></a>Interface do ator

Este contém a definição de interface para o ator. Esta interface define o contrato de ator é partilhado pela execução do ator e os clientes que entra em contacto ator, para que o faz sentido defini-la num local que está separado da execução do ator normalmente e pode ser partilhado por vários outros serviços ou aplicações de cliente.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Serviço do ator 
Este contém a implementação de ator e o código de registo do ator. A classe de ator implementa a interface do ator. Este é o local onde o ator é que o seu trabalho.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Registo do ator

O serviço do ator tem de estar registado junto de um tipo de serviço no runtime do serviço ferro. Ordem no serviço do Ator executar as instâncias do ator, o seu tipo de ator também deve estar registado com o serviço do Ator. O `ActorRuntime` método de registo executa este trabalho aos intervenientes.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {
    
    public static void main(String[] args) throws Exception {
        
        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);
            
        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

### <a name="test-client"></a>Cliente de teste

Esta é uma aplicação de cliente de teste simples pode executar separadamente a partir da aplicação de serviço ferro para testar o serviço do ator. Este é um exemplo de onde o ActorProxy pode ser utilizado para ativar e comunicar com ator instâncias. -Não é implementada com o seu serviço.

### <a name="the-application"></a>A aplicação 

Por fim, a aplicação de pacotes o serviço do ator e quaisquer outros serviços que poderão adicionar no futuro em conjunto para implementação. Marcadores de posição *ApplicationManifest.xml* e colocar para o pacote de serviço do ator nela contidos.

## <a name="run-the-application"></a>Executar a aplicação

O Yeoman andaimes incluem um script de gradle para criar a aplicação e festa de scripts para implementar e un-implementar a aplicação. Para executar a aplicação, crie primeiro a aplicação com gradle:

```bash
$ gradle
```

Isto vai produzir um pacote de aplicação de serviço ferro que pode ser implementado usando o clip do serviço ferro Azure. O script install.sh contém os comandos do Azure clip necessários para implementar o pacote de aplicação. Basta executar o script install.sh para implementar:

```bask
$ ./install.sh
```
