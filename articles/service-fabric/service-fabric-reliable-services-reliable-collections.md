<properties
   pageTitle="Coleções fiáveis | Microsoft Azure"
   description="Serviços com estado do serviço ferro fornecem fiáveis coleções de sites que lhe permite escrever nuvem altamente disponível, dimensionáveis e baixa latência aplicações."
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introdução às coleções de fiável nos serviços com estado ferro de serviço do Azure

Coleções fiáveis permitem-lhe escrever aplicações de nuvem altamente disponível, dimensionáveis e baixa latência, como se estivesse a escrever aplicações único computador. As classes no espaço de nomes **Microsoft.ServiceFabric.Data.Collections** fornecem um conjunto de coleções de out of box tornar o seu estado automaticamente altamente disponível. Os programadores necessitar para o programa apenas a APIs da coleção do fiável e deixar fiável coleções de gerir o estado de replicadas e local.

A diferença entre colecções fiável e outras tecnologias de alta disponibilidade (como Redis, o serviço de tabela do Azure e serviço de fila Azure) chave é que o estado é mantido localmente na instância do serviço enquanto também a ser disponibilizados altamente. Isto significa que:

- Todos os lê é locais, que resulta num latência baixa e alto débito lê.
- Todas as gravações implicam o número mínimo de rede IOs, que resulta numa latência baixa e alto débito escreve.

![Imagem de evolução das coleções de sites.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Coleções fiáveis podem ser consideradas como a evolução natural das **System.Collections** classes: um novo conjunto de coleções de sites que foram concebidos para as aplicações na nuvem e computador com várias sem aumentar complexidade para o programador. Como tal, coleções fiável são:

- Replicadas: Alterações de estado são replicadas para elevada disponibilidade.
- Persistentes: São mantidos dados para o disco para durabilidade contra falhas em grande escala (por exemplo, uma falha de energia de centro de dados).
- Assíncrona: APIs são assíncronas para se certificar de que threads não são bloqueadas quando sempre es.
- Transaccionais: APIs utilizem-las à produção de transações para que possa gerir facilmente várias colecções fiável dentro de um serviço.

Coleções fiáveis fornecem consistência forte garantias terminar a caixa encaminhá-la raciocínio sobre o estado da aplicação mais fácil.
Consistência forte é obtida, certificando-se da transação consolidadas concluída apenas depois de ter a transação inteira foi iniciada um quórum maioria de réplicas, incluindo a página principal.
Para obter mais fraca consistência, aplicações podem reconheça novamente para o cliente/autor da requisição antes da consolidação assíncrona devolve.

As fiável APIs de coleções de sites são uma evolução das coleções em simultâneo APIs (encontrados no espaço de nomes **System.Collections.Concurrent** ):

- Assíncrona: Devolve uma tarefa dado que, ao contrário das coleções em simultâneo, as operações são replicadas e persistentes.
- Parâmetros não de saída: utiliza `ConditionalValue<T>` para devolver um booleano e um valor em vez de parâmetros de saída. `ConditionalValue<T>`é como `Nullable<T>` mas não requer T para ser uma estrutura.
- Transações: Utiliza um objeto da transação para permitir que o utilizador ações de grupo em várias coleções fiável numa transação.

Hoje em dia, **Microsoft.ServiceFabric.Data.Collections** contém duas coleções de sites:

- [Dicionário fiável](https://msdn.microsoft.com/library/azure/dn971511.aspx): representa um conjunto de replicadas, transaccional e assíncrono de pares valor/chave. Semelhante ao **ConcurrentDictionary**, a tecla e o valor podem ser de qualquer tipo.
- [Fila de espera fiável](https://msdn.microsoft.com/library/azure/dn971527.aspx): representa uma replicada, transaccionais e assíncrona estritamente primeiro-in, primeiro saído (FIFO) fila. Semelhante ao **ConcurrentQueue**, o valor pode ser de qualquer tipo.

## <a name="isolation-levels"></a>Níveis de isolamento
Nível de isolamento define o grau às quais a transação deve ser isolada das alterações efetuadas por outras operações.
Existem dois níveis de isolamento que são suportados no fiável coleções de sites:

- **Forma repetida de leitura**: Especifica que declarações não consegue ler dados que foi modificados mas ainda não consolidados por outras operações e que não existem outras operações podem modificar dados que foi lida pela transação atual até que a transação atual termine. Para obter mais detalhes, consulte o artigo [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
- **Instantâneo**: Especifica que dados lidos por qualquer instrução de uma transação será a versão consistente dos dados que existia no início da transação.
A transação pode reconhecer apenas as modificações de dados foram consolidadas antes do início da transação.
Modificações de dados efetuadas por outras operações após o início da transação atual não estão visíveis para declarações em execução na transação atual.
O efeito é como se as instruções numa transação obtém um instantâneo dos dados consolidados, tal como se encontrava no início da transação.
Instantâneos são consistentes em coleções de fiável.
Para obter mais detalhes, consulte o artigo [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Coleções fiáveis automaticamente escolha o nível de isolamento a utilizar para uma operação de leitura determinada consoante a operação e a função da réplica no momento da criação da transação.
Segue-se a tabela que ilustra predefinições de nível de isolamento para operações de dicionário fiável e fila de espera.

| Operação \ função      | Principal          | Secundário        |
| --------------------- | :--------------- | :--------------- |
| Ler entidade única    | Forma repetida leitura  | Instantâneo         |
| Enumeração \ contagem   | Instantâneo         | Instantâneo         |

>[AZURE.NOTE] Exemplos comuns para operações de entidade único são `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.

O dicionário fiável e fila de espera fiável suportam leitura Your escreve.
Por outras palavras, qualquer escrever dentro de uma transação estarão visível num lido seguinte que pertence a mesma transação.

## <a name="locking"></a>O bloqueio
Coleções de fiável, todas as operações são faseados duas: uma transação não liberta bloqueios tenha adquirido até a transação termina com uma interrupção ou uma consolidação.

Dicionário fiável utiliza o bloqueio para todas as operações de única entidade de nível de linha.
Fila de espera fiável equilibra simultaneidade estritamente propriedade FIFO transaccionais.
Fila de espera fiável utiliza bloqueios de nível de operação permitindo uma transação com `TryPeekAsync` e/ou `TryDequeueAsync` e uma transação com `EnqueueAsync` cada vez.
Tenha em atenção que preservar FIFO, se um `TryPeekAsync` ou `TryDequeueAsync` observa alguma vez que a fila fiável está vazia, também bloqueará `EnqueueAsync`.

Escreva operações obtêm sempre os bloqueios exclusivo.
Para operações de leitura, o bloqueio depende do algumas fatores.
Qualquer operação de leitura concluída utilizando instantâneo isolamento é gratuito de bloqueio.
Qualquer operação de forma repetida leitura por predefinição leva bloqueios partilhado.
No entanto, para qualquer operação de leitura que suporte a leitura de forma repetida, o utilizador pode fazer para bloquear uma atualização em vez do bloqueio partilhado.
Bloquear uma atualização é um bloqueio assimétrico utilizado para impedir que uma forma comum de bloqueio que ocorre quando várias transações bloquear recursos para atualizações potenciais tarde.

A matriz de compatibilidade de bloqueio pode ser encontrada abaixo:

| Pedir \ concedido | Nenhum         | Partilhado       | Actualização      | Em modo exclusivo    |
| ----------------- | :----------- | :----------- | :---------- | :----------- |
| Partilhado            | Nenhum conflito  | Nenhum conflito  | Conflito    | Conflito     |
| Actualização            | Nenhum conflito  | Nenhum conflito  | Conflito    | Conflito     |
| Em modo exclusivo         | Nenhum conflito  | Conflito     | Conflito    | Conflito     |

Tenha em atenção que um argumento de limite de tempo nas fiável APIs de coleções de sites é utilizado para deteção de bloqueio.
Por exemplo, duas operações (T1 e T2) estão a tentar ler e actualizar K1.
É possível para que possam criar um impasse, uma vez que estas ambas as terminam ter o bloqueio partilhado.
Neste caso, uma ou ambas as operações irão expirar.

Note que o cenário de bloqueio acima é um excelente exemplo de como bloquear uma atualização pode impedir que os bloqueios.

## <a name="persistence-model"></a>Modelo de persistente
O Gestor de estado fiável e colecções de fiável siga um modelo de persistente denominado registo e ponto de verificação.
Este é um modelo, onde cada alteração de estado é tem sessão iniciada no disco e aplicada apenas memória.
O próprio estado concluído é mantido apenas ocasionalmente (também conhecido como Ponto de verificação).
A vantagem é que deltas são transformados em gravações só para anexo sequenciais no disco para um desempenho melhorado.

Para compreender melhor o modelo de registo e ponto de verificação, vejamos primeiro o cenário de disco infinito.
O Gestor de estado fiável inicia cada operação antes de ser replicada.
Esta opção permite-a coleção de fiável aplicar apenas a operação na memória.
Uma vez que os registos são permanentes, mesmo quando a réplica falha e tem de ser reiniciado, o Gestor de estado fiável tem informações suficientes nos seus registos para repetir todas as operações que a réplica perdeu.
Tal como o disco está infinito, registos de registo nunca terá de ser removido e a coleção de fiável necessita de gerir o estado de na memória.

Agora vamos ver o cenário de disco finita.
Como se acumulem registos do registo, o Gestor de estado fiável será executada fora do espaço em disco.
Antes do que acontece, o Gestor de estado fiável precisa truncar o respectivo registo para arranjar espaço para os registos mais recentes.
-Lo irá pedir o seu estado de na memória no disco as coleções de ficheiros fiável ponto de verificação.
Cabe as coleções de fiável para manter o seu estado por excesso para esse ponto.
Assim que as coleções de ficheiros fiável concluir os respetivos pontos de verificação, o Gestor de estado fiável pode truncar o registo para libertar espaço em disco.
Desta forma, quando a réplica tem de ser reiniciado, coleções fiável irá recuperar o seu estado verificado e o Gestor de estado fiável irão recuperar e reproduzir todas as alterações de estado que ocorreram desde o ponto de verificação.

>[AZURE.NOTE] Adicionar outro valor de pontos de verificação é que melhora o desempenho de recuperação em casos comuns.
Esta é uma vez que os pontos de verificação contenham apenas as versões mais recentes.

## <a name="recommendations"></a>Recomendações

- Não modifique um objeto do tipo personalizado devolvido pela operações de leitura (por exemplo, `TryPeekAsync` ou `TryGetValueAsync`). Coleções fiáveis, tal como faria com coleções em simultâneo, devolvem uma referência para os objetos e não uma cópia.
- Fazer cópia abrangente o objeto devolvido de um tipo de personalizado antes de modificá-lo. Uma vez que as estruturas e tipos de incorporados são fase por valor, não necessita de fazer uma cópia abrangente nos mesmos.
- Não utilize `TimeSpan.MaxValue` para tempos limite. Tempos limite deve ser utilizada para detetar bloqueios.
- Não utilize uma transação depois de ter sido consolidada, interrompida ou eliminado.
- Não utilize uma enumeração fora o âmbito da transação que foi criado.
- Não fazer para criar uma transação dentro de outra transação `using` declaração de uma vez que pode fazer com que bloqueios.
- Certifique-se de que seu `IComparable<TKey>` pós-implementação está correto. O sistema leva dependência nisto para intercalar os pontos de verificação.
- Utilize atualizar bloquear durante a leitura de um item com a intenção de para Atualize-o para impedir que uma determinada classe de bloqueios.
- Considere a utilização de cópia de segurança e restaurar a funcionalidade de ter recuperação de falhas.
- Evite misturar única entidade operações e operações de entidades com várias (por exemplo `GetCountAsync`, `CreateEnumerableAsync`) na mesma transação devido os níveis de isolamento diferente.

Eis algumas coisas a ter em conta:

- O tempo limite predefinido é 4 segundos para todos os APIs coleção fiável. A maioria dos utilizadores não deve substituí-lo.
- O token de cancelamento predefinido é `CancellationToken.None` nas todos os fiável APIs de coleções de sites.
- O parâmetro de tipo de chave (*TKey*) para um dicionário fiável corretamente tem de implementar `GetHashCode()` e `Equals()`. Teclas tem de ser imutáveis.
- Para alcançar elevada disponibilidade para as coleções de fiável, cada serviço deve ter, pelo menos, uma réplica mínima, defina o tamanho de 3 e de destino.
- Operações de leitura no secundária podem ler versões que não estão quórum consolidada.
Isto significa que uma versão de dados que são de leitura a partir de um único secundário poderá ser progresso FALSO.
Obviamente, lê a partir do principal é sempre estável: pode nunca ser falso progresso.

## <a name="next-steps"></a>Próximos passos

- [Guia de introdução do fiável serviços](service-fabric-reliable-services-quick-start.md)
- [Trabalhar com conjuntos fiáveis](service-fabric-work-with-reliable-collections.md)
- [Notificações de serviços fiáveis](service-fabric-reliable-services-notifications.md)
- [Serviços fiáveis cópia de segurança e restaurar (recuperação de falhas)](service-fabric-reliable-services-backup-restore.md)
- [Configuração do Gestor de estado de fiáveis](service-fabric-reliable-services-configuration.md)
- [Introdução ao serviço ferro Web API services](service-fabric-reliable-services-communication-webapi.md)
- [A utilização dos serviços fiável modelo de programação de avançadas](service-fabric-reliable-services-advanced-usage.md)
- [Referência para programadores para coleções de fiável](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
