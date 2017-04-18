<properties
   pageTitle="Configurar a recuperação e recuperação no Elasticsearch no Azure"
   description="Considerações relacionadas com a RDP e recuperação para Elasticsearch."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>
   
# <a name="configuring-resilience-and-recovery-on-elasticsearch-on-azure"></a>Configurar a recuperação e recuperação no Elasticsearch no Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo faz [parte de uma série](guidance-elasticsearch.md). 

Uma funcionalidade de chave do Elasticsearch é o suporte técnico da que fornece RDP trabalho falhas de nós e/ou eventos de partição de rede. Replicação é a forma mais óbvia na qual pode melhorar RDP de qualquer cluster, permitindo-Elasticsearch para se certificar de que mais do que uma cópia de qualquer item de dados está disponível em nós diferentes no caso de um nó deve ficar inacessível. Se um nó ficar temporariamente indisponível, outros nós que contém réplicas de dados a partir do nó em falta podem servir os dados em falta até que o problema seja resolvido. Em caso de um problema de termo mais longo, o nó em falta pode ser substituído por um novo e Elasticsearch pode restaurar os dados para o novo nó de réplicas.

Aqui vamos resumir as opções de recuperação e RDP disponíveis com Elasticsearch quando alojado no Azure e descrevem alguns aspetos importantes de um cluster de Elasticsearch que deverá tomar em consideração para minimizar as hipóteses de perda de dados e os tempos de recuperação de dados expandidos.

Este artigo também ilustra algumas testes de exemplo que foram efetuadas para mostrar os efeitos de diferentes tipos de falhas num Elasticsearch cluster, bem como o sistema responde enquanto recupera.

Um cluster de Elasticsearch utiliza réplicas para manter a disponibilidade e melhorar o desempenho de leitura. Réplicas devem ficar armazenadas nos VMs diferentes a partir dos shards principais que eles criar uma réplica da. A intenção é que se VM alojamento um nó de dados falha ou deixa de estar disponível, o sistema pode continuar a funcionar utilizando VMs mantém as réplicas.

## <a name="using-dedicated-master-nodes"></a>Utilizar dedicadas nós principais

Um nó num Elasticsearch cluster é eleito como o nó do modelo global. É o objetivo deste nó efetuar operações de gestão de cluster tal como:

- Detetar nós falhadas e alterar para o réplicas.

- Reposicionar shards para equilibrar carga de trabalho de nó.

- Recuperar shards quando um nó é colocado novamente online.

Deve considere utilizar dedicadas nós mestras em clusters críticos e certifique-se de que não existem 3 nós dedicada cuja função só deve ser principal. Esta configuração reduz a quantidade de trabalho intenso recurso que têm de executar estes nós (não armazenar dados ou gerir consultas) e ajuda a melhorar a cluster estabilidade. Só irão ser eleito um de nós, mas as outras irão conter uma cópia do Estado do sistema e podem assumir deverá o modelo global de eleito falhar.

## <a name="controlling-high-availability-with-azure--update-domains-and-fault-domains"></a>Controlar elevada disponibilidade com Azure – atualização domínios e de falhas 

Diferentes VMs podem partilhar o mesmo hardware físico. Num centro de dados do Azure, um único bastidor pode alojar um número de VMs e partilham todas estas VMs um parâmetro de origem e de rede power comuns. Uma única falha de nível de bastidor, por conseguinte, pode ter impacto um número de VMs. Azure utiliza o conceito de domínios falhas para tentar e afaste este risco. Um domínio de falhas aproximadamente corresponde a um grupo de VMs que partilham o mesmo bastidor. Para se certificar de que uma falha de nível de bastidor não uma falha de sistema um nó e os nós mantendo todas as suas réplicas em simultâneo, deverá Certifique-se de que o VMs estão distribuídos domínios falhas.

Da mesma forma, VMs podem tomar para baixo o [Azure ferro controlador](https://azure.microsoft.com/documentation/videos/fabric-controller-internals-building-and-updating-high-availability-apps/) para efetuar actualizações de sistema operativo e manutenção planeadas. Azure atribui VMs para atualizar domínios. Quando ocorre um evento de manutenção planeada, são afetados VMs apenas num domínio única actualização em qualquer momento. VMs noutros domínios de atualização são deixados em execução até VMs no domínio de actualização de ser actualizado são colocados online. Por conseguinte, também precisa de para se certificar de que VMs alojamento nós e os respetivos réplicas pertencem a actualização diferente domínios sempre que possível.

> [AZURE.NOTE] Para mais informações sobre domínios falhas e atualização, consulte o artigo [Gerir a disponibilidade de máquinas virtuais][].

Não é possível atribuir explicitamente uma VM para um domínio de falhas e o domínio de actualização específica. Esta atribuição é controlada pelo Azure quando são criadas VMs. No entanto, pode especificar que devem ser criados VMs como parte de um conjunto de disponibilidade. VMs no mesmo conjunto de disponibilidade propagar-se ao longo de actualização de domínios e de falhas. Se criar VMs manualmente, Azure cria cada disponibilidade configurou com dois domínios falhas e cinco de atualização de domínios. VMs são atribuídos a estes domínios falhas e actualização de domínios, ciclo arredondar como ainda mais VMs configurações, da seguinte forma: 

| VM | Domínio de falhas | Atualizar o domínio |
|----|--------------|---------------|
|  1 |            0 |             0 |
|  2 |            1 |             1 |
|  3 |            0 |             2 |
|  4 |            1 |             3 |
|  5 |            0 |             4 |
|  6 |            1 |             0 |
|  7 |            0 |             1 |

> [AZURE.IMPORTANT] Se criar VMs utilizando o Gestor de recursos do Azure, cada conjunto de disponibilidade pode ser atribuído até 3 falhas domínios e atualização de 20. Este é um motivo apelativa para utilizar o Gestor de recursos.

Em geral, coloque VMs todos os que têm o mesmo objectivo no mesmo conjunto de disponibilidade, mas criar conjuntos de disponibilidade diferentes para VMs executam funções diferentes. Com Elasticsearch que isto significa que considere criar disponibilidade, pelo menos, separada define para:

- VMs alojamento nós de dados.
- VMs alojamento nós de cliente (se estiver a utilizá-los).
- VMs alojamento nós principais.

Para além disso, deverá garantir que cada nó num cluster está em consideração o domínio de atualização e falhas domínio-lo ao qual pertence. Estas informações podem ajudar a garantir que Elasticsearch não criar shards e os respetivos réplicas no mesmo falha e atualizar domínios, minimizar a possibilidade de um shard e suas réplicas partir tendo ao mesmo tempo. Pode configurar um nó Elasticsearch aproximadamente a distribuição de hardware do cluster através da configuração de [detecção de alocação shard](https://www.elastic.co/guide/en/elasticsearch/reference/current/allocation-awareness.html#allocation-awareness). Por exemplo, pode definir um par de atributos personalizados nó denominado *faultDomain* e *updateDomain* no ficheiro de elasticsearch.yml, da seguinte forma:

```yaml
node.faultDomain: \${FAULTDOMAIN}
node.updateDomain: \${UPDATEDOMAIN}
```

Neste caso, os atributos são definidos utilizando os valores contidos no * \${FAULTDOMAIN}* e * \${UPDATEDOMAIN}* variáveis de ambiente quando Elasticsearch for iniciado. Também terá de adicionar as seguintes entradas ao ficheiro Elasticsearch.yml para indicar que *faultDomain* *updateDomain* são alocação atributos de detecção e especificar os conjuntos de valores aceitáveis para os seguintes atributos:

```yaml
cluster.routing.allocation.awareness.force.updateDomain.values: 0,1,2,3,4
cluster.routing.allocation.awareness.force.faultDomain.values: 0,1
cluster.routing.allocation.awareness.attributes: updateDomain, faultDomain
```

Pode utilizar detecção de alocação shard em conjunto com a [filtragem de alocação shard](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/shard-allocation-filtering.html#shard-allocation-filtering) para explicitamente especificar quais os nós podem alojar shards para qualquer determinado índice.

Se precisar de dimensionar para além do número de domínios falhas e atualizar um conjunto de disponibilidade, pode criar VMs em conjuntos de disponibilidade adicionais. No entanto, é necessário compreender o que nós nos conjuntos de disponibilidade diferentes podem tomar para baixo para a manutenção em simultâneo. Experimente para se certificar de que cada shard e pelo menos um dos seus réplicas estão contidos dentro do mesmo conjunto de disponibilidade.

> [AZURE.NOTE] Atualmente existe um limite de 100 VMs por disponibilidade conjunto. Para mais informações, consulte o artigo [subscrição Azure e limites de serviço, quotas e as restrições](../azure-subscription-service-limits.md).

### <a name="backup-and-restore"></a>Cópia de segurança e restauro

Utilizar réplicas não fornece protecção completa de falha do grave (como eliminar acidentalmente todo o cluster). Deverá garantir que pode fazer cópia de segurança os dados num cluster regularmente e que tem uma estratégia de Tentei e testada para restaurar o sistema destas cópias de segurança.

Utilize o Elasticsearch [instantâneo e restaurar APIs](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html) : elásticos não maiúscula estes. >> cópia de segurança e restaurar índices. Podem ser guardados instantâneos um sistema de ficheiros partilhado. Em alternativa, plug-ins estão disponíveis que pode escrever instantâneos ao sistema de ficheiros distribuído de Hadoop (HDFS) (o [Plug-in do HDFS](https://github.com/elasticsearch/elasticsearch-hadoop/tree/master/repository-hdfs)) ou ao armazenamento Azure (o [Plug-in do Azure](https://github.com/elasticsearch/elasticsearch-cloud-azure#azure-repository)).

Quando seleciona o mecanismo de armazenamento instantâneo, tenha em consideração os seguintes pontos:

- Pode utilizar o [armazenamento de ficheiros do Azure](https://azure.microsoft.com/services/storage/files/) para implementar um sistema de ficheiros partilhado que esteja acessível a partir de todos os nós.

- Utilize o plug-in HDFS apenas se estiver a executar Elasticsearch em conjunto com Hadoop.

- O plug-in HDFS requer que desativar o Gestor de segurança Java a ser executado dentro da instância Elasticsearch do Java virtual machine (JVM).

- O plug-in HDFS suporta qualquer sistema de ficheiros compatíveis com o HDFS, desde que a configuração de Hadoop correta é utilizada com Elasticsearch.

  
## <a name="handling-intermittent-connectivity-between-nodes"></a>Processamento de conectividade intermitente entre nós

Falhas de rede intermitentes, VM for reiniciado, após manutenção de rotina no Centro de dados e outros eventos semelhantes podem causar nós para se tornar temporariamente inacessível. Nas seguintes situações, onde o evento é provável que seja curta, o overhead da rebalanceamento as shards ocorre duas vezes na introdução sucessão (uma vez quando a falha é detectada e novamente quando o nó ficam visíveis para o modelo global) pode tornar-se uma sobrecarga significativa que impactos desempenho. Pode impedir que inaccessibility nó temporário a causar o modelo global de redistribuir cluster definindo a *atrasada\_tempo limite* propriedade de um índice remissivo, ou para todos os índices. O exemplo abaixo define o atraso para 5 minutos:

```http
PUT /_all/settings
{
    "settings": {
    "index.unassigned.node_left.delayed_timeout": "5m"
    }
}
```

Para mais informações, consulte o artigo [atrasando alocação quando sai um nó](https://www.elastic.co/guide/en/elasticsearch/reference/current/delayed-allocation.html).

Numa rede de que está sujeito às interrupções, também pode modificar os parâmetros que configurar um modelo global para detetar quando outro nó já não está acessível. Estes parâmetros fazem parte do módulo de [deteção de zen](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html#modules-discovery-zen) fornecido com Elasticsearch e, pode defini-las no ficheiro Elasticsearch.yml. Por exemplo, o parâmetro de *discovery.zen.fd.ping.retries* Especifica o número de vezes um nó mestra tentará ping outro nó no cluster antes de decidir que tiver falhado. Este parâmetro assume a predefinição 3, mas pode modificá-la da seguinte forma:

```yaml
discovery.zen.fd.ping_retries: 6
```

## <a name="controlling-recovery"></a>Controlar a recuperação

Quando for restaurada conectividade a um nó após uma falha, qualquer shards nesse nó terão de ser recuperadas para mostrá-los atualizados. Por predefinição, Elasticsearch recupera shards pela seguinte ordem:

- Por data de criação de índice remissivo inversa. Índices mais recentes são recuperados antes de índices mais antigos.

- Por nome de índice remissivo inversa. Índices têm nomes por ordem alfanumérica maiores do que as outras serão restaurados pela primeira vez.

Se alguns índices são críticas mais do que as outras, mas não correspondam a estes critérios pode substituir a precedência de índices, definindo a propriedade *index.priority* . Índices com um valor superior para esta propriedade serão recuperados antes de índices que tenham um valor inferior:

```http
PUT low_priority_index
{
    "settings": {
        "index.priority": 1
    }
}

PUT high_priority_index
{
    "settings": {
        "index.priority": 10
    }
}
```

Para mais informações, consulte o artigo [Prioridade de recuperação de índice remissivo](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/recovery-prioritization.html#recovery-prioritization).

Pode monitorizar o processo de recuperação de um ou mais índices utilizando o * \_recuperação* API:

```http
GET /high_priority_index/_recovery?pretty=true
```

Para mais informações, consulte o artigo [Recuperação de índices](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-recovery.html#indices-recovery).

> [AZURE.NOTE] Um cluster com shards que requerem recuperação terá estado a *amarelo* para indicar que não em todos os shards estão atualmente disponíveis. Quando todas as shards estão disponíveis, o estado de cluster deverá reverter para *verde*. Um cluster com o estado *vermelho* indica que um ou mais shards estão física em falta, poderá ser necessário restaurar os dados a partir de uma cópia de segurança.

## <a name="preventing-split-brain"></a>Impedir o cérebro de divisão 

Um divisão cérebro pode ocorrer se as ligações entre os nós falharem. Se um nó mestra ficar inacessível para a parte do cluster, uma eleições políticas serão executada no segmento de rede que permanece contactáveis e outro nó irão tornar-se o modelo global. Num cluster configurado mal, é possível para cada parte do cluster de ter diferentes modelos globais que resulta em inconsistências de dados ou danos. Este fenómeno é conhecido como uma *divisão cérebro*.

Pode reduzir as hipóteses de uma divisão cérebro configurando a *mínimo\_mestra\_nós* propriedade do módulo deteção, no ficheiro elasticsearch.yml. Esta propriedade especifica nós quantos tem de estar disponíveis para ativar a eleições políticas de uma forma mestre. O exemplo seguinte define o valor desta propriedade para 2:

```yaml
discovery.zen.minimum_master_nodes: 2
```

Este valor deve ser definido para a maioria mais baixa do número de nós que podem desempenhar o papel principal. Por exemplo, se o seu cluster tem 3 nós principais, *mínimo\_mestra\_nós* deve ser definido como 2. Se tiver 5 nós principais, *mínimo\_mestra\_nós* deve ser definido para 3. Idealmente, deverá ter um número ímpar de nós do modelo globais.

> [AZURE.NOTE] É possível para cérebro uma divisão ocorrer se vários nós principais no mesmo cluster são iniciados em simultâneo. Enquanto está rara esta ocorrência, pode impedir que-começando por nós em série um breve atraso (5 segundos) entre cada um deles. 

## <a name="handling-rolling-updates"></a>Processamento de ficha técnica em atualizações

Se estiver a executar uma atualização de software para nós de si próprio (como migrar para uma versão mais recente ou efetuar uma patches), poderá ter desempenhar trabalho em nós individuais que necessita de tomá-los offline ao tempo que mantém o resto do cluster disponível. Esta situação, considere o seguinte processo de execução. 

1. Certifique-se de que reatribuição shard estiver atrasada suficientemente para impedir que o modelo global de eleito rebalanceamento shards a partir de um nó em falta através do resto do cluster. Por predefinição, reatribuição shard estiver atrasada para 1 minuto, mas pode aumentar a duração se um nó é provável que seja indisponível por um período. O exemplo seguinte aumenta o atraso para 5 minutos:

    ```http
    PUT /_all/_settings
    {
        "settings": {
            "index.unassigned.node_left.delayed_timeout": "5m"
        }
    }
    ```

    > [AZURE.IMPORTANT] Também pode desativar reatribuição shard completamente definindo a *cluster.routing.allocation.enable* do cluster para *nenhum*. No entanto, deve evitar utilizar esta abordagem, se novos índices provável que seja criado enquanto estiver offline o nó como isto pode causar uma atribuição de índice falha que resulta num cluster com estado vermelho.

2. Pare Elasticsearch no nó destacado. Se estiver a executar Elasticsearch como um serviço, poderá conseguir parar o processo de uma forma controlada utilizando um comando do sistema operativo. O exemplo seguinte mostra como parar o serviço de Elasticsearch num único nó em execução no Ubuntu:

    ```bash
    service elasticsearch stop
    ```

    Em alternativa, pode utilizar a API encerramento diretamente no nó:

    ```http
    POST /_cluster/nodes/_local/_shutdown
    ```

3. Execute a manutenção necessária no nó

4. Reinicie o nó e aguarde que ele aderir ao cluster.

5. Voltar a ativar alocação shard:

    ```http
    PUT /_cluster/settings
    {
        "transient": {
            "cluster.routing.allocation.enable": "all"
        }
    }
    ```

> [AZURE.NOTE] Se precisar de manter mais do que um nó, repita os passos 2&ndash;4 em cada nó antes de voltar a ativar alocação shard.

Se puder, pare a indexação novos dados durante este processo. Isto irá ajudar a minimizar o tempo quando nós estiverem novamente online e voltar a participar cluster de recuperação.

Tenha em atenção das atualizações automatizadas aos itens tal como o JVM (Idealmente, desativar as atualizações automáticas para estes itens), especialmente quando a ser executado Elasticsearch em Windows. O update agent Java pode transferir a versão mais recente do Java automaticamente, mas pode exigir Elasticsearch ser reiniciado para a atualização entre em vigor. Isto pode resultar na perda temporária descoordenada de nós, dependendo de como o Java Update agent está configurado. Isto também pode resultar em diferentes ocorrências do Elasticsearch no mesmo cluster com diferentes versões da JVM que podem causar problemas de compatibilidade.

## <a name="testing-and-analyzing-elasticsearch-resilience-and-recovery"></a>Testes e analisar recuperação de Elasticsearch e recuperação

Esta secção descreve recuperação de um cluster de Elasticsearch que contém três nós de dados e três nós principais e uma série de testes que tenham sido efetuada para avaliar a resistência.

Foram testados cenários seguintes: 

- Falha de nó e reinicie sem perda de dados. Um nó de dados for interrompido e reiniciado após 5 minutos. Elasticsearch foi configurado para não reatribuir shards em falta neste intervalo, para que não adicionais e/s é realizadas no movimentar shards. Quando o nó for reiniciado, o processo de recuperação traz os shards nesse nó novamente atualizados.

- Falha de nó com perda de dados grave. Um nó de dados está parado e os dados que contém são apagados para simular uma falha do disco grave. O nó é, em seguida, reiniciado (depois de 5 minutos), eficazmente serve como substituição para o nó original. O processo de recuperação requer a reformulação repetida dos dados em falta para este nó e poderá implicar reposicionar shards contidos em outros nós.

- Falha de nó e reinicie sem perda de dados, mas com reatribuição shard. Um nó de dados está parado e são reatribuir as shards que contém a outros nós. O nó, em seguida, for reiniciado e reatribuição mais ocorre a redistribuir o cluster.

- Gradual atualizações. Cada nó no cluster for interrompido e reiniciado após um breve intervalo para simular máquinas a ser reiniciadas após uma atualização de software. Apenas um nó está parado em qualquer momento. Shards não são reatribuir enquanto um nó é premida.

Cada cenário foi sujeito a carga de trabalho mesmo, incluindo uma mistura de dados ingestão tarefas, agregações e consultas de filtro enquanto nós foram tidos offline e recuperados. Em volume inserir operações a carga de trabalho cada 1000 documentos armazenados e foram efetuadas relativamente a um índice enquanto as agregações e consultas de filtro utilizado um índice separado que contenham milhões de vários documentos. Este era ativar o desempenho das consultas ser avaliada separadamente insere o em volume. Cada índice contiver cinco shards e uma réplica.

As secções seguintes resumem os resultados destes testes, verificando qualquer degradação no desempenho enquanto um nó está offline ou a ser recuperadas e erros que foram relatados. Os resultados são apresentados graficamente, realçar os pontos em que um ou mais nós estão em falta e estimar o tempo despendido para o sistema totalmente recuperar e alcançar um nível semelhante do desempenho do que foi apresentar antes dos nós ser tirados offline.

> [AZURE.NOTE] O mateial teste utilizadas para executar estes testes está disponíveis online. Pode adaptar e utilizar estas mateial para verificar a resistência e recuperação do seus próprio configurações de cluster. Para mais informações, consulte o artigo [executar os testes de RDP Elasticsearch automatizados][].

## <a name="node-failure-and-restart-with-no-data-loss-results"></a>Falha de nó e reinicie sem perda de dados: resultados

<!-- TODO; reformat this pdf for display inline --> 

Os resultados deste teste são apresentados no ficheiro [ElasticsearchRecoveryScenario1.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario1.pdf). Os gráficos mostram o perfil de desempenho a carga de trabalho e recursos físicos para cada nó no cluster. A peça inicial dos gráficos Mostrar do sistema em execução normalmente durante cerca de 20 minutos, altura em que está encerrar nó 0 5 minutos antes de ser reiniciado. As estatísticas de mais 20 minutos são ilustradas; o sistema demora cerca de 10 minutos para recuperar e regularização. Isto é ilustrado pelo taxas da transação e tempos de resposta para as cargas de trabalho diferentes.

Tenha em atenção os seguintes pontos:

- Durante o ensaio, não foram foram relatados erros. Não foram perdidos dados e todas as operações foi concluída com êxito.

- As taxas da transação para todos os três tipos de operação (inserir em volume, consulta de agregação e consulta de filtro) largadas e as horas de resposta médio aumentaram enquanto nó 0 estava offline.

- Durante o período de recuperação, as taxas da transação e tempos de resposta para a consulta de agregação e a consulta de filtro operações de forma gradual foram restauradas. O desempenho para inserir em volume recuperados para uma breve enquanto antes de diminuição. No entanto, é provável que devido o volume de dados a causar o índice utilizado pela inserir em volume para aumentar esta e as taxas da transação para esta operação podem ser vistas para diminuir a velocidade mesmo antes de nó 0 é disponibilizado offline.

- O gráfico de utilização da CPU para nó 0 mostra atividade reduzida durante a fase de recuperação, este é devido o disco aumentado e tem de atividade de rede causada pela mecanismo de recuperação, o nó acompanhar com quaisquer dados que tem não atendidas enquanto estiver offline e atualizar os shards nela contidos.

- Shards para os índices exatamente não são distribuídos equitativamente em todos os nós. Existem dois índices que contém 5 shards e 1 réplica cada, tornando um total de 20 shards. Dois nós, por conseguinte, irão conter 6 shards enquanto que as outras duas mantenha 7 cada. Isto é evidente nos gráficos de utilização da CPU durante o período de 20 minutos inicial, nó 0 é menos sobrecarregado que as outras duas. Após a recuperação for concluída, algumas Mudar parece ocorrer como nó 2 é apresentado para se tornar o nó ligeiramente mais carregado.

    
## <a name="node-failure-with-catastrophic-data-loss-results"></a>Falha do nó com perda de dados grave: resultados

<!-- TODO; reformat this pdf for display inline -->

Os resultados deste teste são descritos no ficheiro [ElasticsearchRecoveryScenario2.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario2.pdf). Como com o teste primeiro, a parte inicial dos gráficos mostra o sistema executar normalmente durante cerca de 20 minutos, na qual o nó ponto 0 é encerrar para 5 minutos. Durante este intervalo, os dados de Elasticsearch neste nó são removidos, simulação de perda de dados grave, antes de ser reiniciado. Recuperação completa é apresentada para tirar 12 a 15 minutos antes dos níveis de desempenho visto antes do teste são restaurados.

Tenha em atenção os seguintes pontos:

- Durante o ensaio, não foram foram relatados erros. Não foram perdidos dados e todas as operações foi concluída com êxito.

- As taxas da transação para todos os três tipos de operação (inserir em volume, consulta de agregação e consulta de filtro) largadas e as horas de resposta médio aumentaram enquanto nó 0 estava offline. Neste momento, o perfil de desempenho do teste é semelhante ao primeiro cenário. Não se trata surpreendente como, até ao ponto, os cenários são os mesmos.

- Durante o período de recuperação, as taxas da transação e tempos de resposta foram restaurados, embora durante este período de tempo havido volatilidade muitas mais nos valores. Este é muito provavelmente devido ao trabalho adicional que nós cluster estiver a executar, fornecer os dados para restaurar os shards em falta. Este trabalho adicional é evidente na utilização da CPU, actividade do disco e gráficos de atividade de rede.

- O gráfico de utilização da CPU para nós 0 e 1 mostra atividade reduzida durante a fase de recuperação, que este é devido a aumento no disco e causados pelo processo de recuperação de atividade de rede. No primeiro cenário, só o nó a ser recuperado afixados este comportamento, mas este cenário provavelmente parece que a maior parte dos dados em falta para nó 0 está a ser restaurado a partir nó 1.

- A atividade e/s para nó 0 é realmente reduzida quando comparado com o primeiro cenário. Isto pode ser devido a eficiência e/s de simplesmente copiar os dados para um inteiro shard em vez das séries de mais pequenos pedidos e/s necessários para colocar um shard existente atualizado.

- A atividade de rede para todos os três nós indicam rajada da atividade como dados são transmitidos e recebidos entre nós. Cenário 1, apenas nó 0 afixado como muito actividade de rede, mas esta actividade parece que não aconteceu sofrido por um período. Novamente, esta diferença pode ser devido a eficiência da transmissão de dados inteiros para um shard como um único pedido em vez da série mais pequenos de pedidos de recebidos quando recuperar uma shard.

## <a name="node-failure-and-restart-with-shard-reallocation-results"></a>Falha de nó e reinicie com reatribuição shard: resultados

<!-- TODO; reformat this pdf for display inline -->

O ficheiro [ElasticsearchRecoveryScenario3.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario3.pdf) ilustra os resultados deste teste. Como com o teste primeiro, a parte inicial dos gráficos Mostrar do sistema em execução normalmente durante cerca de 20 minutos, na qual o nó ponto 0 é encerrar para 5 minutos. Neste momento, o cluster Elasticsearch tentativas de recriar os shards em falta e redistribuir os shards em todos os nós restantes. Depois de nó de 5 minutos, 0 é colocado novamente online e, mais uma vez cluster tem a redistribuir os shards. Desempenho é restaurado após 12 a 15 minutos.

Tenha em atenção os seguintes pontos:

- Durante o ensaio, não foram foram relatados erros. Não foram perdidos dados e todas as operações foi concluída com êxito.

- Largadas as taxas da transação para todos os três tipos de operação (inserir em volume, consulta de agregação e consulta de filtro) e os tempos de resposta média aumentaram significativamente enquanto nó 0 estava offline em comparação com os dois testes anterior. Este é devido a atividade de cluster maior recriar os shards em falta e rebalanceamento cluster como representados pelos valores de alto relevo de atividade de disco e de rede para nós 1 e 2 durante este período.

- Durante o período de depois de nó 0 é colocado novamente online, as taxas da transação e tempos de resposta permanecem voláteis.

- Os CPU disco e a utilização da atividade gráficos para nó 0 mostra muito reduzida ação, inicial, durante a fase de recuperação. Isto acontece porque neste momento, nó 0 não serve quaisquer dados. Após um período de aproximadamente 5 minutos, o nó inflama em ação < RBC: esta efetuadas me snort reduzir alto. Posso não estou a chegar para cima uma melhor forma de diga este embora.  >> conforme apresentada pelo aumento repentina sobre da rede, o disco e atividade CPU. Isto é mais provável é causado por cluster redistribuir shards em nós. Nó 0, em seguida, mostra actividade normal.
  
## <a name="rolling-updates-results"></a>Atualizações de gradual: resultados

<!-- TODO; reformat this pdf for display inline -->

Mostram os resultados deste teste, no ficheiro [ElasticsearchRecoveryScenario4.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticSearchRecoveryScenario4.pdf), como cada nó for colocado offline e, em seguida, inseriu novamente novamente sucessão. Cada nó encerrar para 5 minutos antes de ser reiniciado altura em que está parado o nó seguinte numa sequência.

Tenha em atenção os seguintes pontos:

- Enquanto cada nó está desligado e ligado, o desempenho em termos de horas débito e resposta permanece razoável do mesmo.

- Aumenta actividade do disco para cada nó para uma hora abreviada à medida que são colocado novamente online. Este é muito provavelmente devido ao processo de recuperação gradual reencaminhar quaisquer alterações que ocorreram enquanto o nó do era para baixo.

- Quando um nó é disponibilizado offline, picos numa atividade de rede ocorrerem em nós do restantes. Picos também ocorrerem quando um nó é reiniciado.

- Depois do nó final reciclado, o sistema introduz um período de volatilidade significativa. Isto é provavelmente causados pelo processo de recuperação ter de sincronizar alterações entre cada nó e certifique-se de que todas as réplicas e os respetivos shards correspondentes são consistentes. Num ponto, este massa de sucessivas causas esforço inserir operações de tempo limite e falhar. Os erros referidos a cada caso tenham sido:

```
Failure -- BulkDataInsertTest17(org.apache.jmeter.protocol.java.sampler.JUnitSampler$AnnotatedTestCase): java.lang.AssertionError: failure in bulk execution:
[1]: index [systwo], type [logs], id [AVEg0JwjRKxX_sVoNrte], message [UnavailableShardsException[[systwo][2] Primary shard is not active or isn't assigned to a known node. Timeout: [1m], request: org.elasticsearch.action.bulk.BulkShardRequest@787cc3cd]]

```

Pioneira subsequente mostrava que introdução a um atraso de alguns minutos entre ciclo de cada nó eliminados este erro, para que foi provavelmente causados pela contenção entre o processo de recuperação tentar restaurar vários nós em simultâneo e em volume inserir operações tentar armazenar milhares de novos documentos.


## <a name="summary"></a>Resumo

Os testes executados indicado que:

- Elasticsearch foi altamente e são modos mais comuns de falha que podem ocorrer num cluster.

- Elasticsearch pode recuperar rapidamente se um cluster bem concebido está sujeito a perda de dados grave num nó. Isto pode acontecer se configurar Elasticsearch guardar dados ao armazenamento efémero e o nó subsequentemente é reprovisioned após reiniciar o computador. Estes resultados mostram que ainda neste caso, os riscos da utilização do armazenamento efémero são mais prováveis superados pelos benefícios desempenho que fornece esta classe de armazenamento.

- Nos primeiros três cenários, não ocorreram erros no inserir em massa em simultâneo, agregação e cargas de trabalho de consulta de filtro enquanto um nó foi tomado offline e recuperados.

- No último cenário indicado possível perda de dados e esta perda apenas afetadas novos dados a ser adicionados. É aconselhável aplicações efetuar ingestão de dados para mitigar este probabilidade repetindo inserir operações que tenham falhou, tal como o tipo de erro comunicado é altamente provável que seja breves.

- Os resultados do último teste também mostram a que, se estiver a executar a manutenção planeada de nós num cluster, desempenho será vantajoso se permitir vários minutos entre ciclo um nó e o seguinte. Uma situação não planeado (como o Centro de dados Reciclagem nós após efetuar uma atualização do sistema operativo), tem menos controlo sobre como e quando nós são tirados para baixo e reiniciados. A contenção que surge quando tenta Elasticsearch recuperar o estado do cluster depois de corrente nó sequenciais pode resultar em erros e tempos limite. 

[Gerir a disponibilidade de máquinas virtuais]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[Executar os testes de RDP Elasticsearch automatizado]: guidance-elasticsearch-running-automated-resilience-tests.md
