<properties
   pageTitle="Próximos passos de criação do serviço ferro projeto | Microsoft Azure"
   description="Este artigo contém ligações para um conjunto de tarefas de desenvolvimento core para ferro de serviço"
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
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="your-service-fabric-application-and-next-steps"></a>A aplicação de serviço ferro e os passos seguintes
As aplicações do Azure Service ferro foi criada. Este artigo descreve a maquilhagem do seu projeto e alguns passos possíveis.

## <a name="your-application"></a>A aplicação
Cada nova aplicação inclui um projeto de aplicação. Poderão existir um ou dois projetos adicionais, dependendo do tipo de serviço escolhido.

### <a name="the-application-project"></a>O projeto de aplicação
O projeto de aplicação consiste em:

- Um conjunto de referências para os serviços que compõem a sua aplicação.

- Dois publicar perfis (Local e na nuvem) que pode utilizar para manter as preferências para trabalhar com diferentes ambientes – como preferências relacionados com um ponto final de cluster e se quer executar a atualização implementações por predefinição.

- Dois aplicação parâmetro ficheiros (Local e na nuvem) que pode utilizar para manter as configurações de aplicação de ambiente específicas, tais como o número de partições a criar para um serviço.

- Um script de implementação que pode utilizar para implementar a aplicação da linha de comandos ou como parte de um automatizado contínuo integração e implementação em curso.

- Manifesto, da aplicação que descreva a aplicação. Pode encontrar o manifesto na pasta ApplicationPackageRoot.

### <a name="stateless-service"></a>Serviço de estado
Quando adiciona um novo serviço sem estado, o Visual Studio adiciona um projecto de serviço a sua solução que inclui um tipo de descendentes do `StatelessService`. O serviço é incrementado uma variável local num contador.

### <a name="stateful-service"></a>Serviço de estado
Quando adiciona um novo serviço com estado, o Visual Studio adiciona um projecto de serviço a sua solução que inclui um tipo de descendentes do `StatefulService`. O serviço aumenta um contador na sua `RunAsync` método e armazena o resultado num `ReliableDictionary`.

### <a name="actor-service"></a>Serviço do ator
Quando adiciona um novo ator fiável, Visual Studio adiciona dois projetos a sua solução: um projeto do ator e um projeto de interface.

Projeto do ator fornece métodos para definir e obter o valor de um contador que é mantido sujeito dentro de estado do ator. O projeto de interface disponibiliza uma interface que podem utilizar outros serviços para invocar o ator.

### <a name="stateless-web-api"></a>Sem estado Web API
O project Web API sem estado fornece um serviço web básicas que pode utilizar para abrir a sua aplicação para os clientes externos. Para obter mais informações sobre como o projeto estruturado, consulte o artigo [API do serviço Web de ferro os serviços com OWIN personalizada de alojamento](service-fabric-reliable-services-communication-webapi.md).

### <a name="aspnet-core"></a>Principais do ASP.NET

O SDK do serviço ferro fornece o mesmo conjunto de ASP.NET Core modelos que estão disponíveis para autónomo projectos principais ASP.NET: esvaziar, de [Web API][aspnet-webapi]e a [Aplicação Web][aspnet-webapp].

## <a name="next-steps"></a>Próximos passos
### <a name="create-an-azure-cluster"></a>Criar um cluster Azure
O SDK do serviço ferro fornece um cluster local para o desenvolvimento e teste. Para criar um cluster no Azure, consulte [configurar um cluster de serviço ferro a partir do portal do Azure][create-cluster-in-portal].

### <a name="try-deploying-to-azure-for-free-with-party-clusters"></a>Experimente implementá-lo na Azure gratuitamente com clusters de festa

Se quiser experimentar a implementar e gerir aplicações no Azure sem para configurar o seus próprio clusters, pode utilizar o [serviço de cluster de festa](http://aka.ms/tryservicefabric)gratuito.

### <a name="publish-your-application-to-azure"></a>Publicar a sua aplicação ao Azure
Pode publicar a sua aplicação diretamente a partir do Visual Studio para um cluster Azure. Para saber mais, consulte [publicar a sua aplicação ao Azure][publish-app-to-azure].

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>Utilizar o Explorador de ferro de serviço para visualizar o seu cluster
Serviço ferro Explorer oferece uma forma fácil de visualizar o seu cluster, incluindo aplicações e implementados física esquema. Para obter mais informações, consulte o artigo [visualizar o seu cluster utilizando o serviço ferro Explorer][visualize-with-sfx].

### <a name="version-and-upgrade-your-services"></a>Versão e atualizar os seus serviços
Serviço ferro permite independente controlo de versões e efetuar a atualização dos serviços independentes numa aplicação. Para saber mais, consulte o [controlo de versões e atualizar os seus serviços][app-upgrade-tutorial].

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Configurar a integração contínua com os serviços de equipa do Visual Studio
Para saber como pode configurar um processo contínuo integração para a sua aplicação de serviço ferro, consulte [configurar integração contínua com os serviços de equipa do Visual Studio][ci-with-vso].


<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html
