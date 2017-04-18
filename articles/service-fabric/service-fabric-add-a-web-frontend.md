<properties
   pageTitle="Criar um web front-end para sua aplicação utilizando ASP.NET Core | Microsoft Azure"
   description="Expor a aplicação de serviço ferro web utilizando um projeto de ASP.NET Core Web API e a comunicação entre serviço via ServiceProxy."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2016"
   ms.author="seanmck"/>


# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>Criar um serviço front-end web para a sua aplicação utilizando Core do ASP.NET

Por predefinição, os serviços de Azure Service ferro não fornecem uma interface pública para a web. Para expor das funcionalidade da aplicação para os clientes de HTTP, terá de criar um projeto de web para funcionar como um ponto de entrada e, em seguida, comunicar a partir desse local para os seus serviços individuais.

Neste tutorial, iremos continuar parou onde podemos no tutorial [a criar a sua aplicação primeira no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) e adicionar um serviço web à frente o serviço de contador com estado. Se ainda não o tiver feito, deve voltar atrás e siga os passos que tutorial pela primeira vez.

## <a name="add-an-aspnet-core-service-to-your-application"></a>Adicionar um serviço de ASP.NET Core à aplicação

ASP.NET Core é uma estrutura de desenvolvimento de web lightweight, em diferentes plataformas que pode utilizar para criar web moderna IU e APIs da web. Vamos adicionar um projeto de ASP.NET Web API a nossa aplicação existente.

>[AZURE.NOTE] Para concluir este tutorial, terá de [instalar o .NET Core 1.0][dotnetcore-install].

1. No Explorador de solução, com o botão direito **dos serviços** do projeto aplicação e escolha **Adicionar > novo serviço do serviço ferro**.

    ![Adicionar um novo serviço à aplicação existente][vs-add-new-service]

2. Na página **criar um serviço** , selecione **ASP.NET Core** e atribua um nome.

    ![Escolher o serviço web do ASP.NET na caixa de diálogo novo serviço][vs-new-service-dialog]

3. A página seguinte fornece um conjunto de ASP.NET Core modelos do project. Tenha em atenção que estes são os modelos mesmo que pretende ver se criou um projeto de ASP.NET Core fora de uma aplicação de serviço ferro. Para este tutorial, optamos **Web API**. No entanto, pode aplicar os mesmos conceitos a criação de uma aplicação web completo.

    ![A escolher tipo de projeto do ASP.NET][vs-new-aspnet-project-dialog]

    Quando o projeto Web API estiver criado, terá de dois serviços na sua aplicação. Como continuar a criar a sua aplicação, irá adicionar mais serviços exatamente da mesma forma. Cada pode ser atualizado e versões aos forma independente.

>[AZURE.TIP] Para saber mais sobre como construir ASP.NET Core services, consulte a [Documentação do ASP.NET Core](https://docs.asp.net).

## <a name="run-the-application"></a>Executar a aplicação

Para obter uma sensação de tenha feito, vamos implementar a nova aplicação e veja o comportamento predefinido que o modelo de ASP.NET Core Web API fornece.

1. Prima F5 no Visual Studio para depurar a aplicação.

2. Quando implementação estiver concluída, o Visual Studio iniciará browser na raiz do serviço do ASP.NET Web API – algo como http://localhost:33003. O número da porta aleatoriamente está atribuído e pode ser diferente no seu computador. O modelo de ASP.NET Core Web API não fornece comportamento predefinido da raiz, para que irão receber um erro no browser.

3. Adicionar `/api/values` para a localização no browser. Isto vai invocar a `Get` método no ValuesController no modelo de Web API. Irá devolver a resposta predefinida que é fornecida pelo modelo – uma matriz JSON que contém duas cadeias:

    ![Valores predefinidos devolvidos a partir de modelo de ASP.NET Core Web API][browser-aspnet-template-values]

    No final do tutorial, vamos irá ter substituído estes valores predefinidos com o valor de contador mais recente do nosso serviço de estado.


## <a name="connect-the-services"></a>Ligar os serviços

Serviço ferro fornece flexibilidade concluída na como comunicar com os serviços fiáveis. Dentro de uma única aplicação, poderá ter os serviços que estão acessíveis através do TCP, de outros serviços que estão acessíveis através de um HTTP REST API e ainda de outros serviços que estão acessíveis através de web sockets. Para informações gerais sobre as opções disponíveis e as responsabilidades envolvidos, consulte o artigo [Communicating com os serviços](service-fabric-connect-and-communicate-with-services.md). Neste tutorial, iremos siga uma das abordagens mais simples e utilizar o `ServiceProxy` / `ServiceRemotingListener` classes são fornecidas no SDK.

No `ServiceProxy` abordagem (modelada no chamadas de procedimento remoto ou chamadas de procedimento remoto), pode definir uma interface para funcionar como contrato público para o serviço. Em seguida, utilizar essa interface para gerar uma classe de proxy para interagir com o serviço.


### <a name="create-the-interface"></a>Criar a interface

Vamos será iniciado através da criação de interface para funcionar como o contrato entre o serviço de estado e os seus clientes, incluindo o projeto de ASP.NET Core.

1. No Explorador de solução, a solução com o botão direito e selecione **Adicionar** > **Novo projeto**.

2. Selecione a entrada **Visual c#** no painel de navegação do lado esquerdo e, em seguida, selecione o modelo de **Biblioteca de classes** . Certifique-se de que a versão do .NET Framework está definida para **4.5.2**.

    ![Criar um projeto de interface do seu serviço de estado][vs-add-class-library-project]

3. Para que uma interface para ser utilizado pelo `ServiceProxy`, tem de ser a partir da interface de IService. Esta interface está incluída dos pacotes de serviço ferro NuGet. Para adicionar o pacote, o novo projeto de biblioteca de classe com o botão direito e selecione **Gerir pacotes de NuGet**.

4. Procurar o pacote de **Microsoft.ServiceFabric.Services** e instalá-lo.

    ![Adicionar o pacote de serviços NuGet][vs-services-nuget-package]

5. Na biblioteca de classe, criar uma interface com um único método, `GetCountAsync`, e expandir a interface de IService.

    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;

        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```


### <a name="implement-the-interface-in-your-stateful-service"></a>Implementar a interface no seu serviço de estado

Agora que recomendamos tiver definido a interface, precisamos de implementá-lo no serviço de estado.

1. No seu serviço de estado, adicione uma referência ao projeto escolar biblioteca que contém a interface.

    ![Adicionar uma referência para o projeto de biblioteca de classe no serviço de estado][vs-add-class-library-reference]

2. Localize a classe que herda `StatefulService`, tais como `MyStatefulService`, e expandi-lo para implementar o `ICounter` interface.

    ```c#
    using MyStatefulService.Interfaces;

    ...

    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. Agora implementar o método único que está definido na `ICounter` interface, `GetCountAsync`.

    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```


### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>Expor o serviço de estado com uma escuta de comunicação remota de serviço

Com o `ICounter` interface implementada, o passo final activar o serviço de estado ser passível de outros serviços é abrir um canal de comunicação. Para os serviços com estado, o serviço ferro fornece um método overridable denominado `CreateServiceReplicaListeners`. Com este método, pode especificar um ou mais listeners de comunicação, com base no tipo de comunicação que pretende ativar no seu serviço.

>[AZURE.NOTE] O método equivalente para abrir um canal de comunicação para serviços sem estado é chamado `CreateServiceInstanceListeners`.

Neste caso, podemos irá substituir a existente `CreateServiceReplicaListeners` método e forneça uma instância da `ServiceRemotingListener`, que cria um ponto final RPC que é passível de clientes através de `ServiceProxy`.  

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>Utilize a classe de ServiceProxy para interagir com o serviço

O nosso serviço com estado está agora pronto para receber o tráfego de outros serviços. Por isso, tudo o que mantém-se está a adicionar o código para comunicar com o mesmo a partir do serviço web do ASP.NET.

1. No seu projeto do ASP.NET, adicione uma referência para a biblioteca de classes que contém o `ICounter` interface.

2. No menu de **Criar** , abra o **Gestor de configuração**. Deverá visualizar algo parecido com:

    ![Configuração Gestor a mostrar a biblioteca de classes como AnyCPU][vs-configuration-manager]

    Tenha em atenção que o projeto de biblioteca de classe, **MyStatefulService.Interface**, está configurado para construir para qualquer CPU. Para trabalhar corretamente com o serviço ferro,-deve ser explicitamente direcionada para x64. Clique no menu pendente plataforma e selecione **Novo**, em seguida, criar uma x64 configuração de plataforma.

    ![Criar nova plataforma para a biblioteca de classes][vs-create-platform]

3. Adicione o pacote de Microsoft.ServiceFabric.Services ao projeto ASP.NET, tal como anteriores para o projeto de biblioteca de classe. Isto irá fornecer a `ServiceProxy` escolares.

4. Na pasta **controladores** , abra o `ValuesController` escolares. Tenha em atenção que a `Get` método atualmente apenas devolve uma matriz de cadeia codificada de "valor1" e "valor2" – corresponde ao podemos viu anterior no browser. Substitua esta implementação com o seguinte código:

    ```c#
    using MyStatefulService.Interfaces;
    using Microsoft.ServiceFabric.Services.Remoting.Client;

    ...

    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    A primeira linha de código é aquele chave. Para criar o proxy de ICounter para o serviço de estado, tem de fornecer dois blocos de informações: um ID de partição e o nome do serviço.

    Pode utilizar a partições a escala com estado serviços dividindo o seu estado para grupos diferentes, com base numa chave que definir, tal como um ID de cliente ou código postal. Na nossa aplicação comum, o serviço de estado tem apenas uma partição de, para que a tecla é indiferente. Qualquer tecla que fornece, será direcionado para a mesma partição. Para saber mais sobre como criar a partições serviços, consulte o artigo [como partição serviço ferro fiável serviços](service-fabric-concepts-partitioning.md).

    O nome do serviço é um URI de ferro o formulário: /&lt;Nome_aplicação&gt;/&lt;service_name&gt;.

    Com estes dois blocos de informações, o serviço ferro pode identificar exclusivamente a máquina que devem ser enviados pedidos para. O `ServiceProxy` classe totalmente também processa caso de falha do computador que aloja a partições com estado do serviço e outro computador tem de ser promovido a tomar o seu lugar. Este produção faz com que o código do cliente para lidar com outros serviços significativamente mais simples de escrita.

    Assim que temos o proxy, podemos simplesmente invocar a `GetCountAsync` método e devolver o resultado.

5. Prima F5 novamente para executar a aplicação modificada. Como antes, Visual Studio iniciará automaticamente o browser para a raiz do projeto web. Adicione o caminho de "api/valores" e, deverá ver o valor atual de contador devolvido.

    ![O valor do contador com estado apresentado no browser][browser-aspnet-counter-value]

    Atualize o browser periodicamente para visualizar o valor de contador atualizar.


>[AZURE.WARNING] O servidor de web do ASP.NET Core fornecido no modelo, conhecido como Kestrel, é [actualmente não suportadas para processar direta tráfego da internet](https://docs.asp.net/en/latest/fundamentals/servers.html#kestrel). Cenários de produção, considere alojar o seu pontos finais ASP.NET Core atrás de [Gestão de API] [ api-management-landing-page] ou outro gateway de acesso à internet. Tenha em atenção que ferro de serviço não é suportado para implementação do IIS.


## <a name="what-about-actors"></a>E intervenientes?

Neste tutorial com foco nos sobre a adição de um web front-end comunicadas com um serviço de estado. No entanto, pode seguir um modelo muito semelhante a falar com intervenientes. Na verdade, é um pouco mais simples.

Quando cria um projeto do ator, o Visual Studio gera automaticamente um projeto de interface por si. Pode utilizar esse interface para gerar um proxy do ator no projeto web para comunicar com o ator. O canal de comunicação é fornecido automaticamente. Para que não precisam de fazer tudo o que é equivalente ao estabelecer uma `ServiceRemotingListener` como fazer para o serviço com estado neste tutorial.

## <a name="how-web-services-work-on-your-local-cluster"></a>Funcionam de serviços web no seu cluster local

Em geral, pode implementar exatamente a mesmo aplicação de serviço ferro a um cluster de múltiplos máquina implementada no seu cluster local e ter a certeza de vivamente que irão funcionar como esperado. Isto acontece porque o seu cluster local é simplesmente uma configuração de cinco nó estiver fechada para um único computador.

Quando chegar aos serviços web, no entanto, existe uma nuance chave. Quando o seu cluster fica atrás Balanceador de carga, como o faz no Azure, certifique-se de que os serviços web são implementados em todos os computadores, uma vez que o Balanceador de carga será simplesmente round robin tráfego através de máquinas. Pode fazê-lo ao definir a `InstanceCount` no serviço para o valor especial de "-1".

Por outro lado, quando executar um serviço web localmente, precisa para se certificar de que apenas uma instância do serviço está em execução. Caso contrário, irá ocorrer conflitos a partir de vários processos que estão a escutar o caminho e o porta mesmo. Como resultado, deve ser definida a contagem de instância do serviço web "1" para implementações locais.

Para saber como configurar diferentes valores para o ambiente diferente, consulte o artigo [Gerir parâmetros de aplicação para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="next-steps"></a>Próximos passos

- [Criar um cluster no Azure para implementar a aplicação na nuvem](service-fabric-cluster-creation-via-portal.md)
- [Saiba mais sobre a comunicar com os serviços](service-fabric-connect-and-communicate-with-services.md)
- [Saiba mais sobre como criar a partições com estado serviços](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-configuration-manager]: ./media/service-fabric-add-a-web-frontend/vs-configuration-manager.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
[api-management-landing-page]: https://azure.microsoft.com/en-us/services/api-management/
