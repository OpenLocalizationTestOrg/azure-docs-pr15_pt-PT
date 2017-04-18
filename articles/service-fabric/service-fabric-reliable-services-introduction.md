<properties
   pageTitle="Descrição geral do modelo de programação de serviço do serviço ferro fiável | Microsoft Azure"
   description="Saiba mais sobre o modelo de programação do serviço ferro serviço fiável e começar a escrever o seus próprio serviços."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="vturecek; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="masnider;vturecek"/>

# <a name="reliable-services-overview"></a>Descrição geral de serviços fiável
Azure Service ferro simplifica escrita e gerir serviços fiável com e sem a com estado. Neste documento serão falar sobre:

- O modelo de programação fiável serviços para serviços com e sem a com estado.
- As opções que tem de fazer quando escrever um serviço fiável.
- Alguns cenários e exemplos de quando utilizar serviços fiáveis e como são escritas.

Serviços fiáveis é um dos modelos de programação disponíveis no serviço ferro. Para mais informações sobre o modelo de programação intervenientes fiável, consulte o artigo [Introdução ao serviço ferro fiável intervenientes](service-fabric-reliable-actors-introduction.md).

No serviço ferro, um serviço é composto de configuração, código da aplicação e de estado (opcionalmente).

Serviço ferro gere o tempo de vida dos serviços, a partir de aprovisionamento e implementação através da atualização e a eliminação, através de [Gestão de aplicações de serviço ferro](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>O que são serviços fiáveis?
Serviços fiáveis dá-lhe um modelo de programação nível superior, simple e poderoso para ajudá-lo express o que é importante para a sua aplicação. Com os serviços fiável modelo de programação, obtém:

- Para os serviços com estado, o modelo de programação de serviços fiáveis permite-lhe forma consistente e fiável armazenar o direito de estado no interior do seu serviço utilizando fiável coleções de sites. Este é um conjunto simple de classes coleção altamente disponível que será familiares a qualquer pessoa que tenha utilizado c# coleções de sites. Normalmente, os serviços necessárias sistemas externos para gestão de estado fiável. Com fiável coleções de sites, pode armazenar o estado junto ao seu cluster com o mesmo elevada disponibilidade e a fiabilidade que chegou ao sítio esperar de lojas externas altamente disponíveis e com as melhorias de latência adicionais que fornecem cocriação localizar o cluster e o estado.

- Um modelo de simple para executar o seu próprio código que aspeto são utilizadas para os modelos de programação. O código tem um ponto de entrada bem definidos e gerido facilmente ciclo de vida.

- Um modelo de comunicação incorporável. Utilize o transporte da sua escolha, tal como HTTP com [A API do Web](service-fabric-reliable-services-communication-webapi.md), WebSockets, protocolos TCP personalizados, etc. Serviços de fiáveis fornecem alguns excelentes opções out of box pode utilizar ou pode fornecer o seu próprio.

## <a name="what-makes-reliable-services-different"></a>Quais são as especificidades fiável de serviços diferente?
Serviços fiáveis no serviço ferro é diferente do pode ter escrito antes de serviços. Serviço ferro fornece fiabilidade, disponibilidade, consistência e escalabilidade.  

- **Fiabilidade**– seu serviço irá permanecer o mesmo em ambientes não fiáveis onde o seu máquinas poderão falhar ou visitas problemas de rede.

- **Disponibilidade**– seu serviço serão alcançáveis e responder. (Isto não significa que não pode ter os serviços que não podem ser encontrados ou alcançar a partir da fora.)

- **Escalabilidade**– serviços estão desacoplados a partir do hardware específico, e que ele pode aumentar ou encolher conforme necessário através da adição ou remoção de hardware ou recursos virtuais. Serviços de facilmente são divididos (sobretudo nas maiúsculas/minúsculas com estado) para garantir que partes independentes do serviço de podem dimensionar e responder a falhas de forma independente. Por fim, o serviço ferro encoraje serviços para ser lightweight, permitindo milhares de serviços para aprovisionado dentro de um processo simples, em vez de exigir ou dedicar instâncias do SO todas a uma única ocorrência de uma determinada carga de trabalho.

- Pode garantir que sejam consistentes **consistência**– quaisquer informações armazenadas neste serviço (Isto aplica-se apenas aos serviços com estado - mais informações sobre este mais tarde)

## <a name="service-lifecycle"></a>Ciclo de vida do serviço
Se o serviço é com estado ou sem estado, serviços fiável fornecem um ciclo de vida simple que permite-lhe ligue rapidamente o seu código e comece a trabalhar.  Existem apenas um ou dois métodos que precisa para implementar para obter o seu serviço de cima e a ser executado.

- **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - este é o local onde o serviço define a pilha de comunicação que pretende utilizar. A pilha de comunicação, tal como [Web API](service-fabric-reliable-services-communication-webapi.md), é o que define a escutar ponto final ou os pontos finais para o serviço (como clientes irão atinja-lo). Também define como as mensagens que aparecem fim o interagir com os restantes o código de serviço.

- **RunAsync** - este é onde o serviço é executado respectiva lógica de negócio. O que é fornecido token de cancelamento é um sinal para quando deve parar de que funcionam. Por exemplo, se tiver um serviço que necessita de constantemente separar mensagens de uma fila fiável e processá-las, este é sempre que iria acontecer que funcionam.

### <a name="service-startup"></a>Arranque do serviço

Os eventos importantes no ciclo de vida de um serviço fiável são:

1. O objeto de serviço (a coisa que deriva do serviço sem estado ou serviço com estado) fórmula foi criado.

2. O `CreateServiceReplicaListeners` / `CreateServiceInstanceListeners` método é chamado, permitindo-o serviço para devolver uma ou mais listeners de comunicação da sua escolha.
  - Tenha em atenção que isto é opcional, apesar da maioria dos serviços expor algumas ponto final diretamente.

3. Assim que são criadas escutas de comunicação, é aberta.
  - Comunicação ouvintes um método chamado `OpenAsync()`, que é denominado como neste momento e que devolve o endereço de espera para o serviço. Se utiliza o seu serviço fiável um da ICommunicationListeners incorporados, esta é processada por si.

4. Assim que a escuta de comunicação estiver aberta, o `RunAsync()` chama-se método o serviço principal.
  - Tenha em atenção que `RunAsync()` é opcional. Se o serviço é que todos os seus trabalhos diretamente em resposta ao utilizador apenas chamadas, não é necessário para o mesmo implementar o `RunAsync()`.

### <a name="service-shutdown"></a>Encerramento do serviço

Quando o serviço está a ser encerrar (a ser eliminada, atualizado ou foi movido) a ordem de chamada é espelhada: em primeiro lugar, o token de cancelamento contidos por `RunAsync()` é cancelado; em seguida, `CloseAsync()` chama-se no escutas de comunicação.

Existem alguns aspetos importantes a nota sobre encerramento para os serviços com estado:

- Serviço ferro não irá promover outra réplica do seu serviço ao Estado primário até `CloseAsync` e `RunAsync` tenham sido devolvidos. Se estiver a utilizar uma escuta de comunicação incorporados, o `CloseAsync` método é processado por si.

- Enquanto não existe nenhum limite de tempo no devolver a partir destes métodos, perder a capacidade de escrever a colecções fiável imediatamente e, consequentemente, não é possível concluir qualquer trabalho real. Recomenda-se que reponha mais rapidamente possível quando receber um pedido de cancelamento.

## <a name="example-services"></a>Serviços de exemplo
Ao saber este modelo de programação, vamos um breve olhar duas diferentes serviços para ver como estas peças ajustam em conjunto.

### <a name="stateless-reliable-services"></a>Serviços de fiáveis sem estado
Um serviço sem estado é um onde não existem literalmente sem estado mantido dentro do serviço, ou o estado em que está presente é completamente não recuperáveis e não necessita de sincronização, replicação, persistente ou elevada disponibilidade.

Por exemplo, considere uma calculadora que não tem memória e recebe todos os termos e operações a executar ao mesmo tempo.

Neste caso, RunAsync() do serviço de podem estar vazias, uma vez que não haja nenhuma tarefa-processamento de fundo que precisa de fazer o serviço. Quando o serviço de Calculadora é criado, irá devolver uma ICommunicationListener (por exemplo [Web API](service-fabric-reliable-services-communication-webapi.md)) aberto um ponto final de espera na algumas porta. Este ponto final escutar irá ligar para os diferentes métodos (exemplo: "Adicionar (n1, n2)") que definem público API a Calculadora.

Quando uma chamada é feita a partir de um cliente, o método adequado é chamado e o serviço de Calculadora executa operações nos dados fornecidos e devolve o resultado. -Não armazena qualquer Estado.

Não armazenar quaisquer estado interno faz com que esta Calculadora de exemplo muito simples. Mas maioria dos serviços não estão verdadeiramente sem estado. Em vez disso, eles externalize o seu estado para algumas outras arquivo. (Por exemplo, qualquer aplicação web que depende de manter o estado da sessão num armazenamento de segurança ou cache não está completamente sem estado.)

Um exemplo comum de como são utilizados sem estado serviços no serviço ferro está como um front-end que expõe API direcionado para uma aplicação web. O serviço front-end, em seguida, falam serviços com estado para concluir um pedido de utilizador. Neste caso, chamadas a partir dos clientes são dirigidas a uma porta conhecida, tal como 80, onde está a escutar o serviço de estado. Este serviço sem estado recebe a chamada e determina se a chamada está a partir de uma festa de confiança, como bem como qual o serviço é destinado.  Em seguida, o serviço sem estado reencaminha a chamada para a partição correta do serviço de estado e aguarda uma resposta. Quando o serviço sem estado recebe uma resposta, respostas novamente para o cliente original.

### <a name="stateful-reliable-services"></a>Serviços de fiáveis com estado
Um serviço com estado é um valor que tem de ter uma parte do estado mantido consistentes e apresentar para que o serviço de função. Considere a hipótese de um serviço que constantemente fórmula calcula uma média móvel de algum valor com base em atualizações que recebe. Para fazer isto, tem de ter o conjunto atual de pedidos recebidos precisa de processo, bem como a média atual. Qualquer serviço que obtém, processa e armazena informações num arquivo externos (tal como uma Azure blob ou tabela loja hoje) é com estado. Apenas-mantém o seu estado no arquivo de estado externo.

Maior parte dos serviços hoje armazenar o seu estado externamente, uma vez que o arquivo externo é o que fornece fiabilidade, disponibilidade, escalabilidade e consistência para esse Estado. No serviço ferro, serviços com estado não são necessários para armazenar o seu estado externamente; Serviço ferro leva-o até o cuidado destes requisitos para o código de serviço e o estado do serviço.

Digamos que pretendemos escrever um serviço que entra em pedidos de uma série de conversões que têm de ser efetuados numa imagem e a imagem que tem de ser convertida.  Para este serviço-deve devolver uma escuta de comunicação (vamos supor Web API) que é aberto para cima uma porta de comunicação e permite que gostar de submissões através de uma API `ConvertImage(Image i, IList<Conversion> conversions)`. Nesta API, o serviço poderia ter as informações e armazenar o pedido na fila de fiável e em seguida, regresse alguns tokens ao cliente para-pode controlar o pedido (uma vez que os pedidos de poderá demorar algum tempo).

Este serviço RunAsync dever mais complexo. O serviço poderia ter um ciclo dentro da sua RunAsync que obtém pedidos de IReliableQueue, efetua as conversões listadas e armazena os resultados na IReliableDictionary, para que quando o cliente surgir novamente, acedam suas imagens convertidas. Para garantir que o mesmo se algo falha a imagem não é perdida, este serviço fiável seria separar terminar fila de espera, executar as conversões e armazenar o resultado de uma transação. Neste caso, a mensagem é realmente removida apenas de fila de espera e os resultados são armazenados no dicionário resultado quando as conversões estiverem concluídas. Se algo falhar no meio (como o máquina que esta instância do código está em execução no), o pedido permanece na fila em espera para serem processados novamente.

Um critério atenção sobre este serviço é parece de um serviço de .NET normal. A única diferença é que as estruturas de dados a ser utilizadas (IReliableQueue e IReliableDictionary) são fornecidas pelo serviço ferro e, consequentemente, são efetuadas altamente fiável, disponíveis e consistentes.

## <a name="when-to-use-reliable-services-apis"></a>Quando utilizar fiável APIs de serviços
Se qualquer uma das seguintes caracterizar as suas necessidades de serviço de aplicação, em seguida, considere fiável APIs de serviços:

- Tem de fornecer o comportamento da aplicação ao longo de múltiplas unidades de estado (por exemplo, encomendas e ordenar itens de linha).

- Estado da sua aplicação pode naturalmente considerado dicionários fiável e filas.

- Estado da sua tem de estar altamente disponível com o access de latência baixa.

- A aplicação necessitar de controlar a simultaneidade ou granularidade operações transaccionadas numa ou mais coleções fiável.

- Pretende gerir as comunicações ou o esquema de partições do seu serviço de controlo.

- O código necessita de um ambiente do runtime por tópicos livre.

- A aplicação tem de criar ou destroy dicionários fiáveis ou filas o tempo de execução dinamicamente.

- Tem de controlar ferro serviço fornecido pela cópia de segurança e restaurar funcionalidades para o estado * seu serviço através de programação.

- A aplicação tem de manter o histórico de alterações para suas unidades de estado *.

- Pretende desenvolver ou consumir estado personalizado, terceira parte desenvolvido, fornecedores *.

> [AZURE.NOTE] * Funcionalidades disponíveis em disponibilidade geral SDK.


## <a name="next-steps"></a>Próximos passos
+ [Guia de introdução do fiável serviços](service-fabric-reliable-services-quick-start.md)
+ [Avançada a utilização de serviços fiáveis](service-fabric-reliable-services-advanced-usage.md)
+ [O modelo de programação fiável intervenientes](service-fabric-reliable-actors-introduction.md)
