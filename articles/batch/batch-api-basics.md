<properties
    pageTitle="Descrição geral do funcionalidade Batch Azure para programadores | Microsoft Azure"
    description="Obter informações sobre as funcionalidades do seu APIs uma perspectiva de desenvolvimento e o serviço do lote."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/29/2016"
    ms.author="marsma"/>

# <a name="batch-feature-overview-for-developers"></a>Descrição geral da funcionalidade do lote para programadores

Nesta descrição geral dos componentes principais do serviço do Azure lote, discutimos as funcionalidades de serviço principal e recursos que os programadores do lote podem utilizar para criar paralelamente em grande escala calcular soluções.

Se está a desenvolver para uma aplicação utilizaria distribuída ou serviço que problemas direta [REST API] [ batch_rest_api] chamadas ou estiver a utilizar um do [Lote SDK](batch-technical-overview.md#batch-development-apis), que irá utilizar muitos dos recursos e funcionalidades de outros fabricantes referidas neste artigo.

> [AZURE.TIP] Para obter uma introdução complicada ao serviço do lote, consulte o artigo [Noções básicas do Azure lote](batch-technical-overview.md).

## <a name="batch-service-workflow"></a>Fluxo de trabalho do lote serviço

O fluxo de trabalho de alto nível seguinte é típico de quase todas as aplicações e serviços que utilizam o serviço de lote para processamento paralelas das cargas de trabalho:

1. Carregar os **ficheiros de dados** que pretende processar para um [Armazenamento do Windows Azure] [ azure_storage] conta. Lote inclui suporte incorporado para aceder ao armazenamento de Blobs do Azure e as suas tarefas podem transferir estes ficheiros para [Calcular nós](#compute-node) quando as tarefas são executadas.

2. Carregar os **ficheiros da aplicação** que serão executada as suas tarefas. Estes ficheiros podem ser binários ou scripts e as respectivas dependências e são executados pelas tarefas no seu tarefas. As suas tarefas podem transferir estes ficheiros da sua conta de armazenamento ou pode utilizar a funcionalidade de [pacotes de aplicações](#application-packages) do lote para gestão de aplicações e implementação.

3. Crie um [conjunto](#pool) de nós de cluster. Quando cria um conjunto de dados, pode especificar o número de nós de cluster para o conjunto de, o seu tamanho e o sistema operativo. Quando executa a cada tarefa no seu trabalho, atribuiu executar num de nós no seu conjunto.

4. Crie uma [tarefa](#job). Gere a uma tarefa de uma coleção de tarefas. Associar cada tarefa para um conjunto específico de onde serão executada tarefas essa tarefa.

5. Adicione [tarefas](#task) à tarefa. Cada tarefa é executado a aplicação ou script que carregou para os ficheiros de dados transfere da sua conta de armazenamento do processo. Tal como a cada tarefa estiver concluída,-pode carregar respectiva saída ao armazenamento do Azure.

6. Monitorizar o progresso da tarefa e obter o resultado da tarefa a partir do armazenamento do Windows Azure.

As secções seguintes descrevem estes e outros recursos do lote que permitem o seu cenário utilizaria distribuído.

> [AZURE.NOTE] É necessária uma [conta do lote](batch-account-create-portal.md) para utilizar o serviço de lote. Além disso, quase todas as soluções utilizam um [Armazenamento do Windows Azure] [ azure_storage] contabilizar e obtenção de armazenamento de ficheiros. Batch atualmente suporta apenas o **objetivo geral** armazenamento tipo de conta, tal como descrito no passo 5 de [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md).

## <a name="batch-service-resources"></a>Recursos do serviço de batch

Alguns dos seguintes recursos – contas, calcular nós, conjuntos de dados, tarefas e tarefas – forem exigidas pelo todas as soluções que utilizam o serviço de lote. As outras pessoas, como agendas de tarefas e pacotes de aplicações, são úteis, mas opcionais, funcionalidades.

- [Conta](#account)
- [Calcular nó](#compute-node)
- [Agrupamento de](#pool)
- [Tarefa](#job)

  - [Esquemas de projecto](#scheduled-jobs)

- [Tarefa](#task)

  - [Iniciar a tarefa](#start-task)
  - [Tarefa de Gestor de projecto](#job-manager-task)
  - [Tarefas de preparação e lançamento de projecto](#job-preparation-and-release-tasks)
  - [Tarefa com várias instâncias (MPI)](#multi-instance-tasks)
  - [Dependências de tarefas](#task-dependencies)

- [Pacotes de aplicações](#application-packages)

## <a name="account"></a>Conta

Uma conta do lote é uma entidade exclusivamente identificada dentro do serviço de lote. Todo o processamento está associado uma conta do lote. Quando executar operações com o serviço do lote, é necessário o nome de conta e uma das suas chaves de conta. Pode [criar uma conta do lote Azure através do portal Azure](batch-account-create-portal.md).

## <a name="compute-node"></a>Calcular nó

Um nó cluster é uma máquina virtual Azure (VM) é dedicada de uma parte de carga de trabalho da sua aplicação de processamento. O tamanho de um nó determina o número de núcleos CPU, capacidade de memória e tamanho de sistema do ficheiro local que está atribuído a nó. Pode criar conjuntos de dados de nós do Windows ou Linux utilizando serviços em nuvem Azure ou máquinas virtuais Marketplace imagens. Consulte a secção de [agrupamento](#pool) seguinte para obter mais informações sobre estas opções.

Nós podem executar qualquer ficheiro executável ou script que seja suportado pelo ambiente do sistema operativo do nó. Isto inclui \*.exe, \*. cmd, \*. bat e scripts de PowerShell para Windows – e binários, Alçados e Python scripts para Linux.

Calcular todos os nós no lote também incluem:

- Um padrão de [estrutura de pastas](#files-and-directories) e associado [variáveis de ambiente](#environment-settings-for-tasks) que estão disponíveis para referência por tarefas.
- Definições da **Firewall** que estão configuradas para controlar o acesso.
- [Acesso remoto](#connecting-to-compute-nodes) para Windows (Remote Desktop Protocol (RDP)) e nós Linux (seguro Shell (SSH)).

## <a name="pool"></a>Agrupamento de

Um conjunto de dados é uma coleção de nós do que a aplicação é executada no. O conjunto de pode ser criado manualmente por si ou automaticamente pelo serviço lote se indicar o trabalho de ser executadas. Pode criar e gerir um agrupamento que cumpra os requisitos de recursos da sua aplicação. Um conjunto de dados pode ser utilizado apenas na qual foi criada pela conta lote. Uma conta do lote pode ter mais do que um conjunto de dados.

Azure agrupamentos de lote construir na parte superior da plataforma de cluster Azure core. Fornecem alocação em grande escala, instalação da aplicação, distribuição de dados, monitorização do Estado de funcionamento e ajuste flexível do número de nós de cluster dentro de um conjunto de dados ([dimensionamento](#scaling-compute-resources)).

Todos os nós que são adicionado a um conjunto de dados são atribuído um nome exclusivo e o endereço IP. Quando um nó é removido de um conjunto de dados, quaisquer alterações efetuadas para o sistema operativo ou ficheiros serão perdidas e o seu nome e endereço IP são lançadas para utilização futura. Quando um nó deixa um conjunto de dados, a sua vida útil está acima.

Quando cria um conjunto de dados, pode especificar os seguintes atributos:

- Calcular nó **sistema operativo** e **versão**

    Tem duas opções quando seleciona um sistema operativo para os nós no seu conjunto: **Configuração da Máquina Virtual** e **Configuração dos serviços de nuvem**.

    **Configuração de máquina virtual** fornece imagens Linux e Windows para calcular nós partir do [Azure Marketplace máquinas virtuais][vm_marketplace].
    Quando cria um agrupamento que contém nós de configuração de Máquina Virtual, tem de especificar não apenas o tamanho de nós, mas também a **referência de imagem de máquina virtual** e o lote de **agente de nó SKU** instalados em nós. Para mais informações sobre como especificar estas propriedades do conjunto de dados, consulte o artigo [aprovisionar Linux calcular nós no Azure lote conjuntos de dados](batch-linux-nodes.md).

    **Configuração dos serviços de nuvem** fornece que Windows calcular nós *apenas*. Sistemas operativos disponíveis para agrupamentos de configuração dos serviços de nuvem estão listados na [Azure convidado SO lançamentos e da matriz de compatibilidade SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Quando cria um agrupamento que contém nós de serviços em nuvem, tem de especificar apenas o nó e tamanho do seu *Sistema operativo família*. Quando criar conjuntos de dados do Windows calcular nós, normalmente utilizar serviços em nuvem.

    - A *Da família SO* também determina quais as versões do .NET são instaladas com o SO.
    - Como com funções de trabalho dentro serviços em nuvem, pode especificar uma *Versão do SO* (para mais informações sobre funções de trabalho, consulte a secção [informar-me sobre os serviços em nuvem](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services) na [Descrição geral de serviços em nuvem](../cloud-services/cloud-services-choose-me.md)).
    - Como com funções de trabalho, recomendamos que especificar `*` para a *Versão do SO* para que os nós são atualizados automaticamente e não haja nenhuma trabalho necessário para satisfazer para recentemente lançamento de versões. É o caso de utilização principal para selecionar uma versão do SO específica para garantir a compatibilidade de aplicação, que lhe permite retrocompatibilidade testar a ser executado antes de permitir que a versão para ser atualizados. Depois de validação, podem ser atualizados a *Versão do SO* para o agrupamento e a nova imagem SO pode ser instalado – qualquer tarefas em execução são interrompidas e introduzido novamente na fila.

- **Tamanho de nós**

    Tamanhos de nó cluster de **Configuração dos serviços de nuvem** estão listados na [tamanhos dos serviços em nuvem](../cloud-services/cloud-services-sizes-specs.md). Lote suporta todos os tamanhos de serviços em nuvem exceto `ExtraSmall`.

    **Configuração virtual máquina** de calcular nó tamanhos são listados em [tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) e [tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Lote suporta todos os tamanhos de Azure VM exceto `STANDARD_A0` e aqueles com o armazenamento de prémio (`STANDARD_GS`, `STANDARD_DS`, e `STANDARD_DSV2` série).

    Quando seleciona um tamanho de nó cluster, considere as características e os requisitos da aplicações que vai executar em nós. Aspetos como se a aplicação é multithreaded e quanto memória-consome pode ajudar a determinam o tamanho de nó mais adequado e rentável. É típica para selecionar um tamanho de nó assumindo que uma tarefa será executado num nó de cada vez. No entanto, é possível ter várias tarefas (e, consequentemente, várias instâncias da aplicação) [executadas em paralelo](batch-parallel-node-tasks.md) no calcular nós durante a execução da tarefa. Neste caso, é comum para escolher um tamanho de nó maior para acomodar o maior procura da execução da tarefa paralelo. Para obter mais informações, consulte a [política de agendamento de tarefas](#task-scheduling-policy) .

    Todos os nós num conjunto de dados têm o mesmo tamanho. Se pretender executar as aplicações com diferentes requisitos de sistema e/ou carregar níveis, recomendamos que utilize conjuntos de dados separados.

- **Número de destino de nós**

    Este é o número de nós de cluster que pretende implementar no conjunto. Isto é referido como um *destino* porque, em algumas situações, o conjunto de poderá não chegar ao número de nós pretendido. Um conjunto de dados não poderá atingir o número de nós pretendido se atingir a [quota de core](batch-quota-limit.md#batch-account-quotas) para a sua conta do lote – ou se existir uma fórmula de dimensionamento automática que aplicou ao agrupamento de que limita o número máximo de nós (consulte a secção seguinte "Dimensionamento política").

- **Política de dimensionamento**

    Para além de especificando um número estático de nós, em vez disso, pode escrever e aplicar uma [fórmula de dimensionamento automática](#scaling-compute-resources) para um conjunto de. O serviço do lote periodicamente avalia a fórmula e ajusta o número de nós dentro do conjunto de com base em vários agrupamento, tarefa e parâmetros de tarefa que pode especificar.

- **Política de agendamento de tarefas**

    A opção de configuração de [max tarefas por nó](batch-parallel-node-tasks.md) determina o número máximo de tarefas que podem ser executados em paralelo em cada nó cluster dentro do conjunto.

    A configuração predefinida é que uma tarefa, uma vez que é executada num nó, mas existem cenários onde é vantajoso tem mais do que uma tarefa executada num nó em simultâneo. Consulte o artigo o [cenário de exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo para ver como é pode beneficiar a partir de várias tarefas por nó [tarefas nó em simultâneo](batch-parallel-node-tasks.md) .

    Também pode especificar um *tipo de preenchimento* que determina se lote numa ou páginas das tarefas uniformemente em todos os nós num conjunto de dados ou os pacotes de cada nó com o número máximo de tarefas antes de atribuir tarefas a outro nó.

- **Estado de comunicação** de nós do cluster

    Na maioria dos cenários, tarefas funcionam de forma independente em não necessitar de comunicar com um do outro. No entanto, existem algumas aplicações na qual devem comunicar tarefas, como [cenários MPI](batch-mpi.md).

    Pode configurar um conjunto para permitir a comunicação entre os nós dentro it –**comunicação vai**. Quando é activada comunicação vai, nós nos conjuntos de dados de configuração dos serviços de nuvem podem comunicar com os outros em portas maiores 1100 e agrupamentos de configuração de Máquina Virtual não restringir o tráfego em qualquer porta.

    Note que também ativar comunicação vai impactos a colocação de nós de clusters e poderá limitar o número máximo de nós num conjunto de dados devido às restrições de implementação. Se a sua aplicação não requer a comunicação entre nós, o serviço de lote pode atribuir um potencialmente grande número de nós para o conjunto de dados a partir de muitos clusters diferentes e centros de dados para ativar aumentaram processamento paralelas power.

- **Iniciar a tarefa** para cluster nós

    Opcional de *Iniciar a tarefa* é executado em cada nó à medida que o nó associa o agrupamento e sempre que um nó for reiniciado ou reimaged. A tarefa de início é especialmente útil para preparar nós de cluster para a execução das tarefas, como instalar as aplicações que as tarefas são executadas sobre os nós de cluster.

- **Pacotes de aplicações**

    Pode especificar [pacotes de aplicações](#application-packages) para implementar os nós de cluster no conjunto. Pacotes de aplicações fornecem implementação simplificada e controlo de versões das aplicações do que as tarefas são executadas. Pacotes de aplicações que especificou para um conjunto de dados são instalados em cada nó que associa esse agrupamento e cada vez que um nó for reiniciado ou reimaged. Pacotes de aplicações atualmente não são suportadas em nós de cluster Linux.

- **Configuração de rede**

    Pode especificar o ID de uma Azure [rede virtual (VNet)](../virtual-network/virtual-networks-overview.md) na qual o agrupamento calcular nós deve ser criado. Requisitos para especificar uma VNet para o conjunto de pode ser encontrado em [Adicionar um agrupamento de uma conta] [ vnet] na referência lote REST API.

> [AZURE.IMPORTANT] Todas as contas de lote ter uma predefinida **quota** limita o número de **núcleos** (e, consequentemente, nós de cluster) numa conta lote. Pode encontrar as quotas de predefinidas e instruções sobre como para [aumentar uma quota](batch-quota-limit.md#increase-a-quota) (como o número máximo de tarolos na sua conta do lote) na [Quotas e os limites para o serviço do Azure lote](batch-quota-limit.md). Se encontrar o seu próprio perguntar "Por que motivo não meu conjunto alcançar mais de X nós?" Esta quota core poderá ser a causa.

## <a name="job"></a>Tarefa

Uma tarefa é uma coleção de tarefas. Gere a como cálculo é executado pelo respetivas tarefas em nós cluster num conjunto de dados.

- A tarefa Especifica o **agrupamento** na qual o trabalho está a ser executado. Pode criar um novo conjunto para cada tarefa ou utilize um conjunto de dados para muitos tarefas. Pode criar um conjunto de dados para cada tarefa que está associada a uma agenda de trabalho ou para todas as tarefas que estão associadas um agendamento da tarefa.

- Pode especificar um opcional **prioridade tarefa**. Quando uma tarefa é apresentada com uma prioridade mais alta que tarefas que se encontram atualmente em curso, as tarefas para a tarefa de prioridade mais alta são inseridas fila de espera antes da data tarefas para as tarefas de prioridade mais baixa. Não são preemtadas tarefas no prioridade mais baixa tarefas que já estiver a executar.

- Pode utilizar tarefa **restrições** para especificar certos limites para as tarefas:

    Pode definir o **tempo máximo wallclock**, para que se uma tarefa é executada durante mais o tempo máximo wallclock especificado, a tarefa e todas as suas tarefas são terminadas.

    Lote pode detetar e, em seguida, volte a tentar tarefas falhadas. Pode especificar o **número máximo de tentativas de tarefa** como um constrangimento, incluindo se uma tarefa é *sempre* ou *nunca* tentada novamente. Repetir uma tarefa, significa que a tarefa é introduzido novamente na fila a ser executado novamente.

- A aplicação de cliente pode adicionar tarefas a uma tarefa ou pode especificar uma [tarefa de Gestor de projecto](#job-manager-task). Uma tarefa de Gestor de projecto contém as informações que são necessárias para criar as tarefas necessárias para uma tarefa, com a tarefa de Gestor de projecto a ser executada um de nós cluster no conjunto. A tarefa de Gestor de projecto é processada especificamente por lote –-é colocado em fila assim que a tarefa é criada e é reiniciada se falhar. É *necessário* para tarefas que são criados por um [agendamento da tarefa](#scheduled-jobs) , porque é a única forma para definir as tarefas antes da tarefa de criar uma instância uma tarefa de Gestor de tarefa.

- Por predefinição, trabalhos permanecem no estado de ativo quando todas as tarefas na tarefa estiverem concluídas. Pode alterar este comportamento para que a tarefa é terminada automaticamente quando todas as tarefas na tarefa estiverem concluídas. Definir a propriedade de **onAllTasksComplete** a tarefa ([OnAllTasksComplete] [ net_onalltaskscomplete] no lote .NET) para *terminatejob* para terminar automaticamente a tarefa quando todas as suas tarefas estão no estado de concluída.

    Tenha em atenção que o serviço de lote considera que uma tarefa *sem* tarefas de ter todo o das suas tarefas concluídas. Por conseguinte, esta opção com mais frequência é utilizada com uma [tarefa de Gestor de projecto](#job-manager-task). Se pretender utilizar taxas de cessação automática tarefa sem um Gestor de tarefa, pode deve inicialmente defina a propriedade de **onAllTasksComplete** de uma nova tarefa para *noaction*, em seguida, configurá-lo a *terminatejob* apenas depois de terminar de adicionar tarefas à tarefa.

### <a name="job-priority"></a>Prioridade tarefa

Pode atribuir uma prioridade para tarefas que criar no lote. O serviço de lote utiliza o valor prioridade da tarefa para determinar a sequência de programação de trabalhos numa conta (isto não é para ser confundidas com uma [tarefa agendada](#scheduled-jobs)). Os valores de prioridade intervalo entre -1000 e 1000, com a prioridade mais baixa do que está a ser-1000 e 1000 a ser o valor mais alto. Pode atualizar a prioridade de uma tarefa utilizando o [Atualize as propriedades de uma tarefa] [ rest_update_job] operação (lote resto) ou modificando os [CloudJob.Priority] [ net_cloudjob_priority] propriedade (lote .NET).

Na mesma conta, trabalhos de prioridade mais alta tem agendamento precedência sobre tarefas de prioridade mais baixa. Uma tarefa com um valor de prioridade mais alta numa conta não tem a precedência de agendamento sobre outra tarefa com um valor de prioridade mais baixa numa conta diferente.

Tarefa de agendamento nos conjuntos de dados é independente. Entre diferentes conjuntos de dados, não é possível garantir que está agendada primeiro uma tarefa de prioridade mais alta se respectivo conjunto associado for inferior a nós idle. No mesmo conjunto, tarefas com o mesmo nível de prioridade tem uma oportunidade igual de que está a ser agendado.

### <a name="scheduled-jobs"></a>Tarefas agendadas

[Tarefa de agendas] [ rest_job_schedules] permitem-lhe criar tarefas periódicas dentro do serviço de lote. Uma agenda de trabalho especifica quando executar tarefas e inclui as especificações para as tarefas para ser executado. Pode especificar a duração da agenda – quanto tempo e quando a agenda está em efeito - e com que frequência durante este período de tempo que devem ser criadas tarefas.

## <a name="task"></a>Tarefa

Uma tarefa é uma unidade de cálculo que está associado uma tarefa. É executado num nó. Tarefas atribuídas a um nó para execução ou são colocadas na fila até um nó fica gratuito. Colocar simplesmente, uma tarefa é executado um ou mais programas ou scripts um nó cluster para executar o trabalho que precisa.

Quando cria uma tarefa, pode especificar:

- A **linha de comandos** da tarefa. Esta é a linha de comandos que é executada a sua aplicação ou script no nó cluster.

    É importante ter em atenção que a linha de comandos não é realmente executado numa shell. Por conseguinte,-vierem não pode tirar partido das funcionalidades de shell como [variável de ambiente](#environment-settings-for-tasks) de expansão (Isto inclui o `PATH`). Para tirar partido destas funcionalidades, terá de invocar a shell de na linha de comandos – por exemplo, iniciando `cmd.exe` em nós do Windows ou `/bin/sh` no Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Se as suas tarefas precisa para executar uma aplicação ou script que não está no nó `PATH` ou variáveis de ambiente de referência, invocar a shell de explicitamente na linha de comandos de tarefa.

- **Ficheiros de recursos** que contêm os dados a ser processada. Estes ficheiros são automaticamente copiados para o nó do armazenamento de BLOBs numa conta de armazenamento do Windows Azure **finalidade geral** antes de linha de comandos da tarefa é executada. Para mais informações, consulte as secções [Iniciar a tarefa](#start-task) e [ficheiros e directórios](#files-and-directories).

- As **variáveis de ambiente** que forem exigidas pelo seu pedido. Para mais informações, consulte a secção [definições de ambiente de tarefas](#environment-settings-for-tasks) .

- As **restrições** em qual a tarefa deve ser executadas. Por exemplo, o tempo máximo que a tarefa é permitida para executar, o número máximo de vezes que uma falha na tarefa deve ser repetida, e o número máximo de tempo que ficheiros no directório de trabalho da tarefa são retidas.

- **Pacotes de aplicações** para implementar o nó cluster no qual a tarefa é agendada para executar. [Pacotes de aplicações](#application-packages) fornecer implementação simplificada e controlo de versões das aplicações do que as tarefas são executadas. Pacotes de aplicações do nível da tarefa são particularmente útil em ambientes de agrupamento partilhados, onde diferentes tarefas são executadas num conjunto de dados e o agrupamento não é eliminado quando uma tarefa é concluída. Se pretende que a tarefa tem menos tarefas que nós no conjunto, pacotes de aplicações da tarefa podem minimizar transferência de dados, uma vez que a sua aplicação é implementada apenas para os nós que executar tarefas.

Para além das tarefas que pode definir para efetuar cálculo num nó, as seguintes tarefas especiais são também fornecidas pelo serviço lote:

- [Iniciar a tarefa](#start-task)
- [Tarefa de Gestor de projecto](#job-manager-task)
- [Tarefas de preparação e lançamento de projecto](#job-preparation-and-release-tasks)
- [Tarefas com várias instâncias (MPI)](#multi-instance-tasks)
- [Dependências de tarefas](#task-dependencies)

### <a name="start-task"></a>Iniciar a tarefa

Ao associar uma **tarefa de iniciar** um conjunto de dados, pode preparar o ambiente de funcionamento de nós do seus. Por exemplo, pode executar ações como instalar as aplicações que são executadas as suas tarefas e iniciar processos em segundo plano. A tarefa de iniciar é executada sempre que inicia o nó, para desde que mantém-se no conjunto de – incluindo quando o nó estiver em primeiro lugar adicionado ao agrupamento e quando for reiniciado ou reimaged.

Um benefício principal da tarefa iniciar é que podem conter todas as informações que são necessárias para configurar um nó cluster e instalar as aplicações que são necessárias para execução da tarefa. Por conseguinte, aumentar o número de nós num conjunto de dados é tão simple como especificar a contagem de nó de destino novo – lote já tem as informações que são necessário para configurar os nós novos e obtê-lo está preparado para aceitar a tarefas.

Como com qualquer tarefa Azure lote, pode especificar uma lista de **ficheiros de recursos** no [Armazenamento Azure][azure_storage], para além de uma **linha de comandos** para ser executada. Lote pela primeira vez copia os ficheiros de recursos para o nó do armazenamento do Windows Azure e, em seguida, executa a linha de comandos. Para uma tarefa de início do conjunto de dados, a lista de ficheiros contém normalmente o pedido de tarefa e respectivas dependências.

No entanto, também pode incluir dados de referência para ser utilizado por todas as tarefas que estiver a executar no nó cluster. Por exemplo, a linha de comandos de uma tarefa iniciar poderia executar um `robocopy` operação para copiar ficheiros de aplicação (que foram especificados como ficheiros de recursos e são transferidos para o nó) a partir [directório de trabalho da tarefa iniciar](#files-and-directories) para a [pasta partilhada](#files-and-directories)e, em seguida, execute um MSI ou `setup.exe`.

> [AZURE.IMPORTANT] Batch atualmente suporta *apenas* o tipo de conta de armazenamento **Geral objetivo** , conforme descrito no passo 5 de [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md). As tarefas de lote (incluindo tarefas padrão, tarefas de início, tarefas do projecto preparação e tarefas de lançamento do projecto) tem de especificar ficheiros de recursos que residem *apenas* nas contas de armazenamento de **objectivo geral** .

É normalmente conveniente para o serviço do lote de esperar para a tarefa de início concluir antes de considerar o nó pronto para ser atribuída a tarefas, mas poderá configurar esta.

Se uma tarefa de início falhar num cluster nó, em seguida, o estado do nó é atualizado para refletir a falha e o nó não está atribuído todas as tarefas. Uma tarefa de início pode falhar se existir um problema copiar os seus ficheiros de recursos a partir do armazenamento, ou se o processo de feitos à sua linha de comandos devolve um código de saída diferente de zero.

Se adicionar ou atualizar a tarefa de início para um conjunto *existente* , tem de reiniciar o respetivos nós de cluster para a tarefa de início para serem aplicados aos nós.

### <a name="job-manager-task"></a>Tarefa de Gestor de projecto

, Normalmente, utilize uma **tarefa de Gestor de projecto** para controlo e/ou monitorizar a execução de tarefas – por exemplo, para criar e submeter as tarefas para uma tarefa, determinar tarefas adicionais para executar e determinar quando trabalho estiver concluído. No entanto, uma tarefa de Gestor de projecto não está restringida a estas atividades. É uma tarefa totalmente bastante que pode efetuar as ações que são necessárias para a tarefa. Por exemplo, uma tarefa de Gestor de projecto poderá transferir um ficheiro especificado como um parâmetro, analisar o conteúdo desse ficheiro e submeter adicionais tarefas com base nesses conteúdos.

Uma tarefa de Gestor de tarefa é iniciada antes de todas as outras tarefas. Fornece-lhe as seguintes funcionalidades:

- É automaticamente submetida como uma tarefa pelo serviço lote quando a tarefa é criada.

- Agendado para executar antes das outras tarefas numa tarefa.

- O nó associado é o último ser removido de um conjunto de dados quando está a ser reduzido o agrupamento.

- Seu termo pode ser associado a taxas de cessação de todas as tarefas na tarefa.

- Uma tarefa de Gestor de projecto é dado a prioridade mais alta quando necessita de ser reiniciado. Se um nó inactivo não estiver disponível, o serviço do lote pode terminar uma das outras tarefas em execução no conjunto de para arranjar espaço para a tarefa de Gestor de projecto executar.

- Uma tarefa de Gestor de projecto numa tarefa não tem prioridade sobre as tarefas de outras tarefas. Ao longo de tarefas, são observadas apenas prioridades ao nível do trabalho.

### <a name="job-preparation-and-release-tasks"></a>Tarefas de preparação e lançamento de projecto

Lote fornece tarefas do projecto preparação para a configuração de execução de pré-lançamento tarefas. Tarefas de lançamento do projecto destinam-se a tarefa pós manutenção ou limpeza.

- **Tarefa de preparação**: uma tarefa de preparação de trabalho é executado em todos os nós de cluster são agendados para executar tarefas, antes de qualquer uma das outras tarefas de projecto é executada. Pode utilizar uma tarefa de preparação de projecto para copiar os dados que são partilhadas por todas as tarefas, mas são exclusivo para a tarefa, por exemplo.
- **Tarefa de lançamento**: quando uma tarefa estiver concluída, uma tarefa de lançamento do projecto é executado em cada nó no conjunto de que executadas pelo menos uma tarefa. Pode utilizar uma tarefa de lançamento do projecto para eliminar os dados que são copiados da tarefa de preparação do projecto, ou para comprimir e carregue os dados de registo de diagnóstico, por exemplo.

Ambos preparação de trabalho e tarefas de lançamento permitem-lhe especificar uma linha de comandos para ser executada quando a tarefa é chamada. Estas funcionalidades oferecem funcionalidades, como a transferência do ficheiro, execução elevada, variáveis de ambiente personalizado, duração de execução máximo, contagem de tentativas e tempo de retenção do ficheiro.

Para mais informações sobre tarefas de preparação e lançamento de tarefa, consulte o artigo [Executar preparação e de conclusão de tarefas de projecto no Azure lote calcular nós](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Tarefa com várias instâncias

Uma [tarefa com várias instâncias](batch-mpi.md) é uma tarefa que está configurada para executar mais do que um cluster nó em simultâneo. Com tarefas com várias instâncias, pode ativar cenários de computação de alto desempenho que necessitam de um grupo de nós de cluster que são atribuídas em conjunto para processar uma carga de trabalho única (como mensagem prisma Interface (MPI)).

Para informações detalhadas sobre sobre como executar tarefas MPI no lote utilizando a biblioteca de lote .NET, consulte o artigo [tarefas com várias instâncias de utilização para executar as aplicações de mensagem prisma Interface (MPI) no lote Azure](batch-mpi.md).

### <a name="task-dependencies"></a>Dependências de tarefas

[Dependências de tarefas](batch-task-dependencies.md), como o nome implica, permitem-lhe especificar que uma tarefa depende a conclusão de outras tarefas antes da sua execução. Esta funcionalidade fornece suporte para situações em que uma tarefa "descendentes" consome a saída de uma tarefa "montante" – ou quando uma tarefa do montante efetua algumas inicialização necessário por uma tarefa descendentes. Para utilizar esta funcionalidade, tem de ativar dependências de tarefas no seu rotina. Em seguida, para cada tarefa que depende de outro (ou muitas outras), que especificar as tarefas que essa tarefa depende.

Com dependências de tarefas, pode configurar cenários semelhante ao seguinte:

* *taskB* depende *taskA* (*taskB* não começará execução até *taskA* foi concluída).
* *taskC* depende *taskA* e *taskB*.
* *taskD* depende um intervalo de tarefas, tais como tarefas *1* através de *10*, antes de executar.

Dar saída de [dependências de tarefas no lote Azure](batch-task-dependencies.md) e o [TaskDependencies] [ github_sample_taskdeps] código de exemplo nas [amostras de lote azure] [ github_samples] GitHub repositório para obter mais detalhes aprofundadas sobre esta funcionalidade.

## <a name="environment-settings-for-tasks"></a>Definições de ambiente de tarefas

Cada tarefa executada pelo serviço lote tem acesso ao variáveis de ambiente que define nós de cluster. Isto inclui variáveis de ambiente definidas pelo serviço lote ([definidos pelo serviço][msdn_env_vars]) e variáveis de ambiente personalizado que pode definir para as suas tarefas. As aplicações e scripts que executar as tarefas tem acesso a estas variáveis de ambiente durante a execução.

Pode definir variáveis de ambiente personalizado ao nível da tarefa ou tarefa ao preencher a propriedade de *definições de ambiente* para estas entidades. Por exemplo, consulte o artigo [Adicionar uma tarefa a uma tarefa] [ rest_add_task] operação (lote REST API) ou o [CloudTask.EnvironmentSettings] [ net_cloudtask_env] e [CloudJob.CommonEnvironmentSettings] [ net_job_env] propriedades no lote .NET.

Seu serviço ou aplicação cliente pode obter variáveis de ambiente de uma tarefa, definidos pelo serviço e personalizadas, utilizando a [obter informações sobre uma tarefa] [ rest_get_task_info] operação (lote resto) ou acedendo a [CloudTask.EnvironmentSettings] [ net_cloudtask_env] propriedade (lote .NET). Processos em execução num cluster nó podem aceder a estas e outras variáveis de ambiente no nó, por exemplo, utilizando familiares `%VARIABLE_NAME%` (Windows) ou `$VARIABLE_NAME` sintaxe (Linux).

Pode encontrar uma lista completa de todas as variáveis definidos pelo serviço ambiente no [Calcular variáveis de ambiente de nó][msdn_env_vars].

## <a name="files-and-directories"></a>Ficheiros e directórios

Cada tarefa tem uma *directório de trabalho* em qual cria zero ou mais ficheiros e directórios. Este directório de trabalho pode ser utilizado para armazenar o programa que é executado pela tarefa, os dados que processa e o resultado de processamento que executa. Todos os ficheiros e directórios de uma tarefa são propriedade pelo utilizador tarefa.

O serviço do lote expõe uma parte do sistema de ficheiros num nó como o *diretório de raiz*. Tarefas podem aceder ao diretório de raiz, referenciar a `AZ_BATCH_NODE_ROOT_DIR` variável de ambiente. Para mais informações sobre como utilizar variáveis de ambiente, consulte o artigo [definições de ambiente de tarefas](#environment-settings-for-tasks).

O diretório de raiz contém a estrutura do directório seguintes:

![Calcular a estrutura do directório nó][1]

- **partilhada**: este diretório oferece um acesso de leitura/escrita para *todas as* tarefas que são executados num nó. Qualquer tarefa que é executada no nó pode criar, ler, atualizar e eliminar ficheiros neste diretório. Tarefas podem aceder a este diretório, referenciar a `AZ_BATCH_NODE_SHARED_DIR` variável de ambiente.

- **arranque**: este diretório é utilizado por uma tarefa de início, como o directório de trabalho. Todos os ficheiros que são transferidos para o nó pela tarefa de início estão guardados aqui. A tarefa de início pode criar, ler, atualizar e eliminar ficheiros neste diretório. Tarefas podem aceder a este diretório, referenciar a `AZ_BATCH_NODE_STARTUP_DIR` variável de ambiente.

- **Tarefas**: um diretório é criado para cada tarefa que é executada no nó. É acedida por referenciar a `AZ_BATCH_TASK_DIR` variável de ambiente.

    Dentro do diretório de cada tarefa, o serviço de lote cria um diretório de trabalho (`wd`) cujo caminho exclusivo é indicado pela `AZ_BATCH_TASK_WORKING_DIR` variável de ambiente. Este diretório oferece um acesso de leitura/escrita à tarefa. A tarefa pode criar, ler, atualizar e eliminar ficheiros neste diretório. Este diretório é retido com base na restrição *RetentionTime* especificada para a tarefa.

    `stdout.txt`e `stderr.txt`: estes ficheiros são escritos na pasta de tarefas durante a execução da tarefa.

>[AZURE.IMPORTANT] Quando um nó é removido do conjunto, são removidos *todos* os ficheiros que estão armazenados no nó.

## <a name="application-packages"></a>Pacotes de aplicações

A funcionalidade de [pacotes de aplicações](batch-application-packages.md) fornece gestão fácil e implementação de aplicações para os nós de cluster no seu conjuntos de dados. Pode carregar e gerir várias versões das aplicações do executar pelas tarefas, incluindo os seus binários e ficheiros de suporte. Em seguida, automaticamente pode implementar um ou mais destas aplicações para os nós de cluster no seu conjunto.

Pode especificar pacotes de aplicações no nível de agrupamento e a tarefa. Quando especificar pacotes de aplicações do conjunto de dados, a aplicação é implementada para cada nó no conjunto. Quando especificar pacotes de aplicações de tarefa, a aplicação é implementada apenas a nós que estão agendadas para ser executada, pelo menos, uma das tarefas do projecto, antes de linha de comandos da tarefa é executada.

Lote trata os detalhes de trabalhar com o armazenamento do Windows Azure para armazenar os pacotes de aplicações e implementá-los para calcular nós, para que o seu código e a gestão de sobrecarga pode ser simplificada.

Para saber mais sobre a funcionalidade de pacote de aplicação, consulte o artigo [implementação de aplicações com o Azure lote pacotes de aplicações](batch-application-packages.md).

>[AZURE.NOTE] Se adicionar pacotes de aplicações do conjunto de dados para um conjunto *existente* , tem de reiniciar o seus nós de cluster os pacotes de aplicações ser implementada para os nós.

## <a name="pool-and-compute-node-lifetime"></a>Conjunto de dados e cluster nó durante a vida.

Quando estrutura a solução lote Azure, tem de tomar uma decisão de estrutura sobre como e quando os conjuntos de dados são criados e calcular quanto tempo são mantidos nós dentro esses conjuntos de dados à disposição.

Das extremidades pretenderem, pode criar um conjunto de dados para cada tarefa submetidos e eliminar o conjunto de dados, assim que as suas tarefas de conclusão da execução. Isto maximiza utilização porque os nós apenas são atribuídos quando for necessário e encerrar assim que estejam idle. Enquanto Isto significa que a tarefa tem de aguardar os nós a serem atribuídos, é importante ter em atenção que as tarefas são agendadas para execução assim que nós estão individualmente disponíveis, atribuídos, e a tarefa de início foi concluída. Lote é que *não* Aguarde até que o todos os nós dentro de um conjunto de dados estão disponíveis antes de atribuir tarefas a nós. Este procedimento garante utilização máxima de todos os nós disponíveis.

Na outra extremidade pretenderem, se está a ter de tarefas iniciar imediatamente é a prioridade mais alta, pode criar um conjunto de antecedência e disponibilizar os nós antes de tarefas são apresentadas. Neste cenário, podem começar imediatamente a tarefas, mas nós poderão sentar idle enquanto aguardá-los a serem atribuídas.

Uma abordagem combinada é normalmente utilizada para o processamento de uma variável, mas em curso, carga. Pode ter um agrupamento que são submetidas para várias tarefas, mas podem dimensionar o número de nós para cima ou para baixo de acordo com a tarefa de carrega (consulte [dimensionamento calcular recursos](#scaling-compute-resources) na secção seguinte). Pode fazê-lo sobrecarregar, com base no caso de carga atual ou importante, se carga pode ser prevista.

## <a name="scaling-compute-resources"></a>Dimensionamento de cluster de recursos

Com [escala automática](batch-automatic-scaling.md), pode ter o serviço de lote dinamicamente ajustar o número de nós de cluster num conjunto de acordo com a utilização de carga de trabalho e de recursos atual do seu cenário de cluster. Esta opção permite-lhe baixar o custo total da sua aplicação em execução utilizando apenas os recursos que necessita, e libertar aquelas de que não precisa.

Ativar a escala automática ao escrever uma [fórmula de dimensionamento automática](batch-automatic-scaling.md#automatic-scaling-formulas) e associar essa fórmula com um conjunto de dados. O serviço de lote utiliza a fórmula para determinar o número de destino de nós do grupo para o intervalo de dimensionamento seguinte (um intervalo que pode configurar). Pode especificar as definições de dimensionamento para um conjunto de dados quando criá-lo ou ativar o dimensionamento num conjunto de dados mais tarde. Também pode atualizar as definições de dimensionamento num conjunto de dados com capacidade de dimensionamento.

Por exemplo, talvez uma tarefa requer que submeter um grande número de tarefas para ser executada. Pode atribuir uma fórmula de dimensionamento para o conjunto de ajusta o número de nós no conjunto de com base no número atual de tarefas em fila de espera e a taxa de conclusão de tarefas na tarefa. O serviço do lote periodicamente avalia a fórmula e redimensiona o conjunto de, com base em carga de trabalho (adicionar nós para muitas tarefas em fila de espera e remover nós para sem tarefas em fila ou em execução) e as definições de fórmulas.

Uma fórmula de dimensionamento pode basear as métricas seguintes:

- **Métricas de tempo** são baseados em estatísticas recolhidas em 5 minutos no número especificado de horas.

- **Métricas de recursos** se baseiam a utilização da CPU, a utilização de largura de banda, a utilização de memória e número de nós.

- **Métricas de tarefa** são baseados no estado da tarefa, tal como *ativa* (na fila), *Executar*ou *concluídas*.

Quando escala automática reduz o número de nós de cluster num conjunto de dados, tem de considerar como lidar com tarefas que estiver a executar no momento da operação de diminuir. Para acomodar isto, o lote fornece uma *opção de desatribuição nó* que pode incluir nas fórmulas. Por exemplo, pode especificar que tarefas em execução são paradas imediatamente, paradas imediatamente e, em seguida, introduzidas para execução noutro nó ou tem permissão para ser concluída antes do nó é removido do conjunto.

Para mais informações sobre dimensionamento automaticamente uma aplicação, consulte o artigo [automaticamente escala calcular nós num conjunto de dados lote Azure](batch-automatic-scaling.md).

> [AZURE.TIP] Para maximizar a utilização de recursos de cluster, defina o número de destino de nós do zero no final de uma tarefa, mas permitir a execução de tarefas para concluir.

## <a name="security-with-certificates"></a>Segurança com certificados

Normalmente, tem de utilizar certificados quando encriptar ou desencriptar informações confidenciais para tarefas, como a chave para uma [conta de armazenamento do Windows Azure][azure_storage]. Para suportar esta funcionalidade, pode instalar os certificados em nós. Segredos encriptados são transmitidos para tarefas através de parâmetros da linha de comandos ou incorporados um dos recursos de tarefa e os certificados instalados podem ser utilizados para desencriptá-los.

Utilize o [certificado de adicionar] [ rest_add_cert] operação (lote resto) ou [CertificateOperations.CreateCertificate] [ net_create_cert] método (lote .NET) para adicionar um certificado para uma conta do lote. Em seguida, pode associar o certificado para um conjunto de dados novo ou existente. Quando um certificado está associado um conjunto de dados, o serviço do lote instala o certificado em cada nó no conjunto. O serviço do lote instala os certificados adequados quando o nó é iniciado, antes de iniciar quaisquer tarefas (incluindo a tarefa de início e a tarefa de Gestor de projecto).

Se adicionar certificados para um conjunto *existente* , tem de reiniciar o respetivos nós de cluster para os certificados para serem aplicados aos nós.

## <a name="error-handling"></a>Processamento de erros

Pode achar necessárias para lidar com tarefas e aplicação falhas dentro da sua solução lote.

### <a name="task-failure-handling"></a>Processamento de falha da tarefa
As falhas de tarefa de se situar por estas categorias:

- **Falhas de agendamento**

    Se a transferência de ficheiros que estão especificados para uma tarefa falhar por qualquer motivo, um "erro agendamento" está definido para a tarefa.

    Agendamento erros pode ocorrer se os ficheiros da tarefa de recursos ter passado, a conta de armazenamento já não se encontra disponível ou outro problema tiver sido encontrou que impedido a cópia bem sucedida de ficheiros para o nó.

- **Falhas de aplicações**

    O processo que é indicado pelo linha de comandos da tarefa também pode falhar. Considera-se que o processo de ter falhado quando um código de saída diferente de zero, é devolvido pelo processo de que é executado pela tarefa (consulte *códigos de saída de tarefa* , na secção seguinte).

    Para falhas de aplicações, pode configurar o lote automaticamente repetir a tarefa até um número especificado de vezes.

- **Falhas de constrangimento**

    Pode definir um constrangimento que especifica a duração de execução máximo de uma tarefa ou de uma tarefa, o *maxWallClockTime*. Isto pode ser útil para pôr termo tarefas "suspenso".

    Quando a quantidade de tempo máxima tenha sido ultrapassada, a tarefa é marcada como *concluída*, mas o código de saída está definido para `0xC000013A` e o campo *schedulingError* é assinalado como `{ category:"ServerError", code="TaskEnded"}`.

### <a name="debugging-application-failures"></a>Depuração falhas de aplicações

- `stderr`e`stdout`

    Durante a execução, uma aplicação poderá produzir saída diagnóstico que pode utilizar para resolver problemas. Tal como mencionado na secção anterior [ficheiros e directórios](#files-and-directories), o serviço de lote escreve saída padrão e saída de erro-padrão para `stdout.txt` e `stderr.txt` ficheiros no diretório tarefa no nó cluster. Pode utilizar o portal do Azure ou dos SDK lote para transferir estes ficheiros. Por exemplo, pode obter estes e outros ficheiros para fins de resolução de problemas utilizando [ComputeNode.GetNodeFile] [ net_getfile_node] e [CloudTask.GetNodeFile] [ net_getfile_task] na biblioteca do lote .NET.

- **Códigos de saída de tarefa**

    Como mencionado anteriormente, uma tarefa é marcada como falhou pelo serviço lote se o processo que é executado pela tarefa devolve um código de saída diferente de zero. Quando uma tarefa executa um processo, lote preenche a propriedade de código de sair da tarefa com o *código do processo de retorno*. É importante ter em atenção que o código de sair de uma tarefa é **não** determinado pelo serviço lote –-é determinada pelo processo de si próprio ou o sistema operativo no qual o processo executado.

### <a name="accounting-for-task-failures-or-interruptions"></a>Gestão de contas para falhas de tarefa ou interrupções

Tarefas, ocasionalmente, poderão falhar ou ser interrompidas. A própria aplicação tarefa poderá falhar, poderá ser reiniciado o nó no qual a tarefa está a ser executado ou o nó poderão ser removido do conjunto de durante uma operação de redimensionamento se a política de desatribuição o agrupamento está definida para remover nós imediatamente sem aguardar tarefas concluir. Em todos os casos, a tarefa pode ser automaticamente introduzido novamente na fila por lote para execução noutro nó.

Também é possível para um problema intermitente fazer com que uma tarefa deixar de responder ou tomar demasiado tempo a executar. Pode definir o tempo de execução máximo de uma tarefa. Se for excedido-lo, lote interrompe o pedido de tarefa.

### <a name="connecting-to-compute-nodes"></a>Ligar-se para calcular nós

Pode executar depuração e resolução de problemas ao iniciar sessão para um nó cluster remotamente adicionais. Pode utilizar o portal do Azure para transferir um ficheiro do protocolo de ambiente de trabalho remoto (RDP) para os nós do Windows e obtenha informações de ligação de Shell seguro (SSH) para os nós Linux. Pode também fazê-lo ao utilizar a API do lote – por exemplo, com o [Lote .NET] [ net_rdpfile] ou o [Lote Python](batch-linux-nodes.md#connect-to-linux-nodes).

>[AZURE.IMPORTANT] Para ligar a um nó através de RDP ou SSH, tem de criar um utilizador no nó. Para executar esta tarefa, pode utilizar o Azure portal, [Adicionar uma conta de utilizador para um nó] [ rest_create_user] ao utilizar a API do resto lote, ligar para o [ComputeNode.CreateComputeNodeUser] [ net_create_user] método no lote .NET ou chamada de [add_user] [ py_add_user] método no módulo lote Python.

### <a name="troubleshooting-bad-compute-nodes"></a>Resolução de problemas "bad" calcular nós

Em situações onde algumas das suas tarefas estão a falhar, o seu serviço ou aplicação de cliente do lote pode examinar os metadados das tarefas falhadas para identificar um nó com problemas. Cada nó num conjunto de dados é atribuído um ID exclusivo e o nó no qual uma tarefa é executado está incluído nos metadados tarefa. Depois de ter identificado um nó do problema, que pode tomar várias ações com o mesmo:

- **Reinicie o nó** ([REST][rest_reboot] | [.NET][net_reboot])

    Reiniciar o nó, por vezes, pode limpar o latentes problemas como processos presa ou falhou. Tenha em atenção que se o conjunto de utiliza uma tarefa de início ou a tarefa de uma tarefa de preparação do projecto, estas são executadas quando o nó for reiniciado.

- **Reimage o nó** ([REST][rest_reimage] | [.NET][net_reimage])

    Este procedimento reinstala o sistema operativo no nó. Tal como acontece com reiniciar um nó, iniciar tarefas e tarefas de preparação da tarefa são execute novamente depois do nó foi reimaged.

- **Remover o nó do conjunto de** ([REST][rest_remove] | [.NET][net_remove])

    Por vezes, é necessário remover completamente o nó do conjunto de.

- **Desativar o nó de agendamento de tarefas** ([REST][rest_offline] | [.NET][net_offline])

    Isto eficazmente leva-o até o nó "offline" para que não existem novas tarefas atribuídas ao mesmo, mas permite que o nó para permanecer em execução e no conjunto. Isto permite-lhe executar aprofundar para a causa das falhas sem perder os dados da tarefa falha – e sem o nó a causar falhas de tarefa adicional. Por exemplo, pode desativar de agendamento no nó, em seguida, [Inicie sessão no remotamente](#connecting-to-compute-nodes) para examinar registos dos eventos do nó ou realizar outras de resolução de problemas de tarefas. Depois de concluir a sua inquérito, em seguida, pode trazer o nó novamente online ao ativar o agendamento de tarefas ([resto][rest_online] | [.NET][net_online]), ou faça uma das outras ações debatidas anteriormente.

> [AZURE.IMPORTANT] Com cada ação que é descrita nesta secção – reiniciar o computador, reimage, remover e desativar agendamento de tarefas – não está capaz de especificar tarefas atualmente em execução no nó de que forma são processadas quando executar a ação. Por exemplo, quando desativar agendamento de tarefas num nó utilizando a biblioteca do cliente lote .NET, pode especificar um [DisableComputeNodeSchedulingOption] [ net_offline_option] o valor de enumeração para especificar se para **Terminar** a executar tarefas, **Requeue** -las para um agendamento em outros nós ou permitir a execução de tarefas para concluir antes de executar a ação (**TaskCompletion**).

## <a name="next-steps"></a>Próximos passos

- Guiá-lo através de uma aplicação do lote de exemplo passo a passo no [artigo Introdução ao Azure lote biblioteca para o .NET](batch-dotnet-get-started.md). Também existe uma [versão Python](batch-python-tutorial.md) do que é executada uma carga de trabalho no Linux nós de cluster tutorial.

- Transferir e criar o [Lote Explorer] [ github_batchexplorer] project de exemplo para utilização enquanto desenvolver soluções o lote. Utilizando o Explorador do lote, pode efetuar o seguinte e muito mais:
  - Monitorizar e manipular conjuntos de dados, tarefas e tarefas existentes na sua conta do lote
  - Transferir `stdout.txt`, `stderr.txt`e outros ficheiros de nós
  - Criar utilizadores em nós e transferir ficheiros RDP para o início de sessão remoto

- Saiba como [criar conjuntos de dados de nós do cluster Linux](batch-linux-nodes.md).

- Visite o [Fórum do Azure lote] [ batch_forum] no MSDN. O fórum é um bom local para fazer perguntas, se está apenas a aprender ou são um especialista num utilizando lote.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
