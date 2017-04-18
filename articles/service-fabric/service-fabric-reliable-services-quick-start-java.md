<properties
   pageTitle="Começar a trabalhar com serviços fiáveis | Microsoft Azure"
   description="Introdução à criação de uma aplicação do Microsoft Azure Service ferro com os serviços com e sem a com estado."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="vturecek"/>

# <a name="get-started-with-reliable-services"></a>Começar a trabalhar com serviços fiáveis

> [AZURE.SELECTOR]
- [C# no Windows](service-fabric-reliable-services-quick-start.md)
- [Java no Linux](service-fabric-reliable-services-quick-start-java.md)

Este artigo explica as noções básicas do Azure Service ferro fiável Services e orienta-o a criar e implementar uma aplicação de serviço fiável simple escrita em Java.

## <a name="installation-and-setup"></a>Instalação e configuração
Antes de começar, certifique-se de que tem o ambiente de desenvolvimento do serviço ferro configurar no seu computador.
Se precisar de configurá-lo, aceda a [introdução no Mac](service-fabric-get-started-mac.md) ou [Linux começar a trabalhar](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a utilizar com os serviços fiável, apenas terá compreender alguns conceitos básicos:

 - **Tipo de serviço**: Esta é a sua aplicação de serviço. É definido pelo escolares que escreve que se expande `StatelessService` e outro código ou dependências utilizadas nele, juntamente com um nome e um número de versão.

 - **Com o nome a instância do serviço**: para executar o seu serviço, deverá criar instâncias com nome do tipo de serviço, muito como criar instâncias de objeto de um tipo de classe. Instâncias do serviço de facto são instâncias de objeto da sua aula de serviço que escreve. 

 - **Anfitrião do serviço**: as instâncias de serviço nomeado criar precisa para executar o dentro de um anfitrião. O anfitrião do serviço é apenas um processo onde podem executar instâncias do seu serviço.

 - **Registo do serviço**: registo conjuga tudo. O tipo de serviço tem de estar registado com o serviço ferro runtime num anfitrião do serviço para permitir que o serviço ferro criar instâncias do mesmo para executar.  

## <a name="create-a-stateless-service"></a>Criar um serviço de estado

Comece por criar uma nova aplicação de serviço ferro. O SDK do ferro de serviço para Linux inclui um Yeoman gerador para fornecer andaime para uma aplicação de serviço ferro com um serviço de estado. Inicie executando Yeoman o seguinte comando:

```bash
$ yo azuresfjava
```

Siga as instruções para criar um **Estado de serviço fiável**. Para este tutorial, o nome da aplicação "HelloWorldApplication" e o serviço de "HelloWorld". O resultado irá incluir directórios para o `HelloWorldApplication` e `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>Implementar o serviço

Abra **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Esta classe define o tipo de serviço e pode executar qualquer código. A API do serviço fornece dois pontos de partida para o seu código:

 - Um método de ponto de entrada aberta, chamado `runAsync()`, onde pode começar já a qualquer das cargas de trabalho, incluindo cluster de execução longa cargas de trabalho em execução.

```java
@Override
protected CompletableFuture<?> runAsync() {
    ...
}
```

 - Ponto de entrada de comunicação onde pode Plug-in a pilha de comunicação de escolha. Este é onde pode começar a receber pedidos de utilizadores e outros serviços.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Neste tutorial, vamos irá focar o `runAsync()` método do ponto de entrada. Este é onde pode começar imediatamente a executar o seu código.

### <a name="runasync"></a>RunAsync

A plataforma chamadas este método quando uma instância de um serviço colocado e está pronto para executar. O ciclo de abrir/fechar de uma instância do serviço pode ocorrer muitas vezes ao longo do tempo de vida do serviço de como um todo. Isto pode acontecer por vários motivos, incluindo:

- O sistema move as instâncias do serviço de balanceamento de recurso.
- Ocorrem falhas no seu código.
- A aplicação ou sistema é atualizado.
- O hardware subjacente experiências uma falha.

Este orchestration é gerido pelo serviço ferro para manter o seu serviço altamente disponíveis e desequilibradas corretamente.

#### <a name="cancellation"></a>Cancelamento

É crucial que o código no `runAsync()` pode parar a execução quando notificado por ferro de serviço. O `CompletableFuture` devolvido por `runAsync()` é cancelado quando serviço ferro requer o seu serviço para parar a execução. O exemplo seguinte demonstra como gerir um evento de cancelamento: 

```java
    @Override
    protected CompletableFuture<?> runAsync() {

        CompletableFuture<?> completableFuture = new CompletableFuture<>();
        ExecutorService service = Executors.newFixedThreadPool(1);
        
        Future<?> userTask = service.submit(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                try
                {
                   logger.log(Level.INFO, this.context().serviceName().toString());
                   Thread.sleep(1000);
                }
                catch (InterruptedException ex)
                {
                    logger.log(Level.INFO, this.context().serviceName().toString() + " interrupted. Exiting");
                    return;
                }
            }
         });
 
        completableFuture.handle((r, ex) -> {
            if (ex instanceof CancellationException) {
                userTask.cancel(true);
                service.shutdown();
            }
            return null;
        });
 
        return completableFuture;
   }
``` 

### <a name="service-registration"></a>Registo do serviço

Tipos de serviço tem de estar registados com o tempo de execução do serviço ferro. O tipo de serviço está definido na `ServiceManifest.xml` e a sua aula de serviço que implementa `StatelessService`. Registo do serviço é executado no ponto de entrada principal de processo. Neste exemplo, o ponto de entrada principal de processo é `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    } 
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration: {0}", ex.toString());
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Executar a aplicação

O Yeoman andaimes incluem um script de gradle para criar a aplicação e festa de scripts para implementar e un-implementar a aplicação. Para executar a aplicação, crie primeiro a aplicação com gradle:

```bash
$ gradle
```

Isto vai produzir um pacote de aplicação de serviço ferro que pode ser implementado usando o clip do serviço ferro Azure. O script install.sh contém os comandos do Azure clip necessários para implementar o pacote de aplicação. Basta executar o script install.sh para implementar:

```bask
$ ./install.sh
```
