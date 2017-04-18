<properties
   pageTitle="Arquitetura de um serviço fiável | Microsoft Azure"
   description="Descrição geral da arquitetura de um serviço fiável para os serviços com estado e sem estado"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/30/2016"
   ms.author="alanwar"/>

# <a name="architecture-for-stateful-and-stateless-reliable-services"></a>Arquitetura para serviços fiável com estado e sem estado

Um Azure ferro fiável serviço poderá estar com estado ou sem estado. Cada tipo de serviço é executado dentro de uma arquitetura de específica. Estes arquitecturas são descritas neste artigo.
Consulte o artigo [Descrição geral de um serviço fiável](service-fabric-reliable-services-introduction.md) para obter mais informações sobre as diferenças entre os serviços com estado e sem estado.

## <a name="stateful-reliable-services"></a>Serviços de fiáveis com estado

### <a name="architecture-of-a-stateful-service"></a>Arquitetura de um serviço de estado
![Diagrama de arquitectura de um serviço de estado](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### <a name="stateful-reliable-service"></a>Serviço fiável com estado

Um serviço fiável com estado pode deriva classe a StatefulService ou StatefulServiceBase. Ambos destas base classes são fornecidos pelo serviço ferro. Estas funcionalidades oferecem vários níveis de suporte e de produção para o serviço de estado para a interface com serviço ferro – e para participar como um serviço dentro do cluster de serviço ferro.

StatefulService deriva da StatefulServiceBase. StatefulServiceBase oferece serviços mais flexibilidade, mas requer mais compreensão dos princípios internos do serviço ferro.
Consulte a [Descrição geral de um serviço fiável](service-fabric-reliable-services-introduction.md) e [a utilização de avançadas de um serviço fiável](service-fabric-reliable-services-advanced-usage.md) para mais informações sobre as especificidades de escrita services utilizando as classes StatefulService e StatefulServiceBase.

Ambas as classes base gerem o tempo de vida e a função de implementação do serviço. A aplicação de serviço poderá substituir virtuais métodos de um dos classe base se a aplicação de serviço tiver trabalho a fazer nessas alturas no ciclo de vida de aplicação de serviço – ou se pretende criar um objeto de escuta comunicação. Tenha em atenção que, apesar de uma aplicação de serviço poderá implementar a sua própria objeto de escuta de comunicação expor ICommunicationListener, no diagrama acima, a escuta de comunicação é implementada pelo serviço ferro – tal como a aplicação de serviço utiliza uma escuta de comunicação que é implementada através do serviço ferro.

Um serviço fiável com estado utiliza o Gestor de estado fiável para tirar partido das coleções fiáveis. Coleções fiáveis são estruturas de dados local altamente disponíveis para o serviço – que está, são sempre disponíveis, independentemente de activações pós-falha de serviço. Cada tipo de coleção de fiável é implementado por um fornecedor de estado fiável.
Para mais informações sobre fiáveis coleções de sites, consulte o artigo [Descrição geral de fiável coleções de sites](service-fabric-reliable-services-reliable-collections.md).

### <a name="reliable-state-manager-and-state-providers"></a>Gestor de estado fiável e fornecedores de estado

O Gestor de estado fiável é o objeto que faz a gestão dos fornecedores de estado fiável. -Possui a funcionalidade para criar, eliminar, enumerar e certifique-se de que os fornecedores de estado fiável estão persistentes e altamente disponíveis. Uma instância do fornecedor Estado fiável representa uma instância de uma estrutura de dados persistentes e altamente disponíveis, tal como um dicionário ou uma fila.

Fornecedor de cada Estado fiável expõe uma interface que é utilizada por um serviço de estado para interagir com o fornecedor de estado fiável. Por exemplo, IReliableDictionary é utilizado para interface com o dicionário fiável, enquanto IReliableQueue é utilizada para interface com a fila fiável. Todos os fornecedores de estado fiável implementam a interface de IReliableState.

O Gestor de estado fiável tem uma interface denominada IReliableStateManager, que permite o acesso à mesma a partir de um serviço de estado. Interfaces para fornecedores de estado fiável são devolvidas através de IReliableStateManager.

O Gestor de estado fiável utiliza uma arquitetura de plug-in para que os novos tipos de coleções de fiáveis podem ser ligados dinamicamente.

O dicionário fiável e fila fiável foram criadas após a execução de um arquivo de diferença de alto desempenho, com versões.

### <a name="transactional-replicator"></a>Replicação transaccional

O componente de replicação transaccionais é responsável por assegurar que o estado de um serviço (ou seja, o estado dentro o Gestor de estado fiável e as coleções de ficheiros fiáveis) está consistente em todas as réplicas a executar o serviço. Também assegura que o estado é mantido no registo de. As interfaces do Gestor de estado fiável com a replicação transaccional através de um mecanismo privado.

A replicação transaccional utiliza um protocolo de rede para comunicar o estado com outras réplicas da instância do serviço para que todas as réplicas tenham informações de estado atualizado.

A replicação transaccional utiliza um registo para manter informações de estado para que as informações de estado survives processo ou falha de nó. A interface para o registo é através de um mecanismo privado.

### <a name="log"></a>Registo

O componente log fornece um arquivo de persistente de alto desempenho pode ser otimizado para escrita para rodar um Estado sólidos discos ou.  A estrutura do registo é para o armazenamento persistente (por exemplo, discos rígido) ser local para os nós que estiver a executar o serviço de estado. Esta opção permite-para latências baixos e débito alto, em comparação com armazenamento persistente remoto, não é local para o nó.

O componente log utiliza vários ficheiros de registo. Existe um nó wide partilhada ficheiro de registo que todas as réplicas utilizam como pode fornecer a latência mais baixa e mais alto débito para armazenar dados de estado. Por predefinição, o registo de partilhada é colocado do diretório de trabalho do serviço ferro nó mas podem também ser configurada seja colocado na outra localização, idealmente num disco reservada para apenas o registo partilhado. Cada réplica para o serviço também tem um ficheiro de registo dedicada e o registo de dedicada é colocado dentro do diretório de trabalho do serviço. Não existe nenhuma mecanismos para configurar o registo de dedicada seja colocado numa localização diferente.

O registo partilhado é uma área de transição para informações de estado a réplica, enquanto o ficheiro de registo dedicado está o destino final onde é mantido. Nesta estrutura, as informações de estado são escritas pela primeira vez para o ficheiro de registo partilhado e, em seguida, lazily, movidas para o ficheiro de registo dedicado em segundo plano. Desta forma, a escrita para o registo de partilhada teria a latência mais baixa e mais alto débito que permite que o serviço tornar o progresso mais rápido.

Lê e são feitas escritas para o registo de partilhada através do es direta pré-atribuídos espaço no disco do ficheiro de registo partilhado. Para permitir que uma utilização ideal da espaço em disco na unidade de com registos dedicadas, o ficheiro de registo dedicado é criado como um ficheiro disperso NTFS. Tenha em atenção que isto irá permitir overprovisioning de espaço no disco e o sistema operativo irá apresentar os ficheiros de registo dedicado utilizando o espaço em disco muito mais do que realmente é utilizado.

Para além de uma interface mínimas do modo de utilizador para o registo, o registo é escrito como um controlador de modo de núcleo. Executando como um controlador de modo de núcleo, o registo pode fornecer o mais alto desempenho a todos os serviços que utilizá-la.

Para mais informações sobre como configurar o registo, consulte o artigo [Configurar serviços de fiáveis com estado](service-fabric-reliable-services-configuration.md).

## <a name="stateless-reliable-service"></a>Serviço fiável sem estado

### <a name="architecture-of-a-stateless-service"></a>Arquitetura de um serviço de estado
![Diagrama de arquitectura de um serviço de estado](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### <a name="stateless-reliable-service"></a>Serviço fiável sem estado

Implementações do serviço sem estado derivam da classe de StatelessService ou StatelessServiceBase. A classe de StatelessServiceBase permite mais flexibilidade do que a classe de StatelessService.
Ambas as classes base gerem o tempo de vida e a função de um serviço.

A aplicação de serviço poderá substituir virtuais métodos de um dos classe base se o serviço foi trabalho para fazer a esses pontos o ciclo de vida do serviço – ou se pretende criar um objeto de escuta de comunicação. Tenha em atenção que, apesar do serviço poderá implementar a sua própria objeto de escuta de comunicação expor ICommunicationListener, no diagrama acima, a escuta de comunicação é implementada pelo serviço ferro, à medida que a aplicação serviço utiliza uma escuta de comunicação que é implementada através do serviço ferro.

Consulte a [Descrição geral de um serviço fiável](service-fabric-reliable-services-introduction.md) e [a utilização de avançadas de um serviço fiável](service-fabric-reliable-services-advanced-usage.md) para mais informações sobre as especificidades de serviços utilizando as classes StatelessService e StatelessServiceBase a escrever.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos

Para mais informações sobre ferro de serviço, consulte:

[Descrição geral de um serviço fiável](service-fabric-reliable-services-introduction.md)

[Guia de introdução](service-fabric-reliable-services-quick-start.md)

[Descrição geral de fiável coleções de sites](service-fabric-reliable-services-reliable-collections.md)

[Serviço fiável a utilização de avançadas](service-fabric-reliable-services-advanced-usage.md)

[Configuração de um serviço fiável](service-fabric-reliable-services-configuration.md)  
