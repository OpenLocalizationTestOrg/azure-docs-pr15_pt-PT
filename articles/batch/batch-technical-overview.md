<properties
    pageTitle="Noções básicas de serviço do Azure lote | Microsoft Azure"
    description="Saiba como utilizar o serviço do Azure lote para paralelamente em grande escala e das cargas de trabalho HPC"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/22/2016"
    ms.author="marsma"/>

# <a name="basics-of-azure-batch"></a>Noções básicas do lote Azure

Azure lote permite-lhe executar as aplicações em grande escala alto desempenho e paralelas computação (HPC) eficazmente na nuvem. É um serviço da plataforma que agenda com muitos cluster de trabalho para ser executado numa coleção de geridos das máquinas virtuais e pode automaticamente escala calcular recursos para satisfazer as necessidades das suas tarefas.

Com o serviço do lote, pode definir Azure cluster recursos para executar as aplicações em paralelo e em escala. Pode executar a pedido ou agendada tarefas e não precisa de manualmente criar, configurar e gerir um cluster HPC, máquinas virtuais individuais, redes virtuais ou uma tarefa complexa e infraestrutura de agendamento de tarefas.

## <a name="use-cases-for-batch"></a>Casos de utilização para lote

Lote é um serviço Azure gerido que é utilizado para *processamento batch* ou *lote computação*– executar um grande volume de tarefas semelhantes para obter algumas resultado pretendido. Lote computação com mais frequência é utilizado pelas organizações que processam regularmente, transformam e analisar grandes volumes de dados.

Lote funciona bem com condições, paralelas aplicações (também conhecido como "embarrassingly paralelas") e das cargas de trabalho. Condições, paralelas das cargas de trabalho facilmente estão divididas em várias tarefas que executam o trabalho em simultâneo em vários computadores.

![Paralelo de duas tarefas][1]<br/>

Alguns exemplos de cargas de trabalho que normalmente são processados utilizando esta técnica são:

* Modelação financeira risco
* Clima e hidrologia análise de dados
* Processamento, análise e composição de imagem
* Codificação de multimédia e transcodificação
* Análise de sequência genética
* Análise de limite de engenharia
* Testes de software

Lote também pode efetuar cálculos paralelos com um passo de reduzir no final e executar mais complexas HPC das cargas de trabalho como [Mensagem prisma Interface (MPI)](batch-mpi.md) aplicações.

Para uma comparação entre o lote e outras opções de solução HPC no Azure, consulte o [lote e HPC soluções](batch-hpc-solutions.md).

## <a name="developing-with-batch"></a>Desenvolver com o lote

Processamento paralelas das cargas de trabalho com o lote é normalmente feito através de programação utilizando uma das [APIs lote](#batch-development-apis). Com a API do lote, pode criar e gerir conjuntos de dados de nós do cluster (máquinas virtuais) e agendar tarefas e tarefas para executar em nós. Uma aplicação de cliente ou serviço que o utilizador é o criador utiliza APIs do lote para comunicar com o serviço do lote.

Pode eficazmente processo das cargas de trabalho em grande escala da sua organização ou fornecer um front-end de serviço aos seus clientes, para que estes podem ser executados projectos e tarefas de – a pedido ou numa agenda – num, centenas ou mesmo milhares de nós. Também pode utilizar o lote como parte de um fluxo de trabalho maior, gerido pelo ferramentas, como o [Azure fábrica de dados](../data-factory/data-factory-data-processing-using-batch.md).

> [AZURE.TIP] Quando estiver pronto para procurar de forma a API do lote para uma forma mais aprofundada compreensão das funcionalidades-fornece, consulte o artigo [Descrição geral da funcionalidade do lote para programadores](batch-api-basics.md).

### <a name="azure-accounts-youll-need"></a>Terá de contas do Azure

Quando desenvolver soluções de lote, irá utilizar as seguintes contas no Microsoft Azure.

- **Conta azure e de subscrição** , se ainda não tiver uma subscrição do Azure, pode ativar o seu [subscritor MSDN beneficiar][msdn_benefits], ou Inscreva-se para uma [conta Azure gratuita][free_account]. Quando criar uma conta, uma subscrição predefinido é criada por si.

- **Conta lote** - quando as suas aplicações interagem com o serviço do lote, o nome da conta, o URL da conta e uma tecla de acesso são utilizados como credenciais. Todos os recursos lote como conjuntos de dados, calcular nós, tarefas, e tarefas estão associadas uma conta do lote. Pode [criar conta lote](batch-account-create-portal.md) no portal do Azure.

- **Conta de armazenamento** - lote inclui suporte incorporado para trabalhar com ficheiros de [Armazenamento do Windows Azure][azure_storage]. Cenário quase todas as lote utiliza armazenamento do Windows Azure – para os programas que são executadas as suas tarefas e os dados que serem processados de transição e para o armazenamento dos dados de saída geram. Para criar uma conta de armazenamento, consulte o artigo [sobre Azure contas de armazenamento](./../storage/storage-create-storage-account.md).

### <a name="batch-development-apis"></a>Desenvolvimento lote APIs

As aplicações e serviços podem emitir diretas REST API chamadas, utilizar um ou mais dos seguintes bibliotecas de cliente ou uma combinação de ambas para gerir calcular recursos e das cargas de trabalho de paralelas executar na escala através do serviço do lote.

| API    | Referência da API | Transferir | Exemplos de código |
| ----------------- | ------------- | -------- | ------------ |
| **Lote resto** | [MSDN][batch_rest] | N/D | [MSDN][batch_rest] |
| **Lote .NET**    | [MSDN][api_net] | [NuGet][api_net_nuget] | [GitHub][api_sample_net] |
| **Batch Python**  | [readthedocs.IO][api_python] | [PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Batch Node.js** | [github.IO][api_nodejs] | [npm][api_nodejs_npm] | - |
| **Lote Java** (pré-visualização) | [github.IO][api_java] | [Maven][api_java_jar] | [GitHub][api_sample_java] |

### <a name="batch-resource-management"></a>Gestão de recursos do lote

Para além do cliente APIs, também pode utilizar o seguinte procedimento para gerir os recursos existentes na sua conta do lote.

- [Batch os cmdlets do PowerShell][batch_ps]: O lote de Azure cmdlets no módulo [Azure PowerShell](../powershell-install-configure.md) permitem-lhe gerir lote recursos com o PowerShell.

- [Clip do Azure](../xplat-cli-install.md): A Interface de comandos Azure (Azure CLI) é um conjunto de ferramentas em diferentes plataformas que fornece comandos da shell para interagir com vários serviços Azure, incluindo o lote.

- Biblioteca de cliente do [Lote gestão .NET](batch-management-dotnet.md) : também disponíveis através de [NuGet][api_net_mgmt_nuget], pode utilizar a biblioteca do cliente lote gestão .NET para através de programação gerir contas lote, quotas e pacotes de aplicações. Referência para a biblioteca de gestão é no [MSDN][api_net_mgmt].

### <a name="batch-tools"></a>Ferramentas de batch

Enquanto não for necessário criar soluções que utilizam o lote, aqui estão algumas ferramentas útil para utilizar durante a criação e de depuração suas lote aplicações e serviços.

 - [Azure portal][portal]: pode criar, monitorizar e eliminar o lote conjuntos de dados, tarefas e tarefas no pás de lote o portal Azure. Pode ver as informações de estado para estes e outros recursos enquanto executar as tarefas de e até mesmo transferir ficheiros a partir de nós cluster os agrupamentos de dados (transferir uma tarefa falha `stderr.txt` durante a resolução de problemas, por exemplo). Também pode transferir os ficheiros de ambiente de trabalho remoto (RDP) que pode utilizar para iniciar sessão para calcular nós.

 - [Azure lote Explorer][batch_explorer]: lote Explorer disponibiliza lote recurso gestão uma funcionalidade semelhante como o portal do Azure, mas num programa autónomo a aplicação de cliente do Windows Presentation Foundation (WPF). Uma das aplicações de exemplo do lote .NET disponíveis no [GitHub][github_samples], pode criá-lo com Visual Studio 2015 ou acima e utilizá-lo para procurar e gerir os recursos na sua conta do lote enquanto desenvolver e depurar as soluções do lote. Tarefa de vista, agrupamento e detalhes da tarefa, transferir ficheiros de nós de cluster e ligarem a nós remotamente com ficheiros de ambiente de trabalho remoto (RDP) que pode transferir com o Explorador de lote.

 - [Explorador de armazenamento do Microsoft Azure][storage_explorer]: enquanto não estritamente uma ferramenta de Azure lote, o Explorador de armazenamento é outra ferramenta útil para ter enquanto está a desenvolver e depuração as soluções do lote.

## <a name="scenario-scale-out-a-parallel-workload"></a>Cenário: Escala saída uma carga de trabalho paralela

Uma solução comuns que utiliza o lote APIs para interagir com o serviço do lote envolve escalar para fora trabalho condições, paralelo – tal como a composição de imagens para cenas 3D – num conjunto de nós de cluster. Este conjunto de nós do cluster pode ser o "composição do farm de servidores" que fornece dezenas, centenas ou mesmo milhares de núcleos para o seu trabalho composição, por exemplo.

O diagrama seguinte mostra um comuns lote fluxo de trabalho, com uma aplicação de cliente ou serviço alojado utilizando o lote para executar uma carga de trabalho paralela.

![Fluxo de trabalho do lote solução][2]

Neste cenário comuns, o seu serviço ou aplicação processos uma carga de trabalho utilizaria no Azure lote executando os seguintes passos:

1. Carregar os **ficheiros de entrada** e da **aplicação** que processará esses ficheiros à sua conta de armazenamento do Windows Azure. Ficheiros de entrada podem ser quaisquer dados que a aplicação irá processar, tal como modelação financeira em dados, ou ficheiros de vídeo para ser transcodificados. Os ficheiros da aplicação podem ser qualquer aplicação que é utilizada para processar os dados, tal como uma aplicação de composição de 3D ou transcoder de multimédia.

2. Criar um lote de **agrupamento** de nós do cluster na sua conta do lote estes nós são as máquinas virtuais que serão executadas as suas tarefas. Especificar propriedades, tais como o [tamanho de nó](./../cloud-services/cloud-services-sizes-specs.md), o seu sistema operativo e a localização de armazenamento do Windows Azure da aplicação para instalar quando os nós participar o agrupamento de (a aplicação que carregou no passo #1). Também pode configurar o agrupamento para [Dimensionar automaticamente](batch-automatic-scaling.md)– dinamicamente ajustar o número de nós de cluster no conjunto de – em resposta à carga de trabalho que as suas tarefas geram.

3. Crie uma **tarefa** de lote para executar a carga de trabalho no conjunto de nós de cluster. Quando cria uma tarefa, associá-la com um conjunto de comandos.

4. Adicione **tarefas** à tarefa. Quando adiciona tarefas para uma tarefa, o serviço do lote automaticamente agenda as tarefas para execução em nós cluster no conjunto. Cada tarefa utiliza a aplicação que carregou para processar ficheiros de entrada.

    - 4. Antes de executa uma tarefa, pode transferir a dados (ficheiros de entrada) que é o processo para o nó cluster que ter sido atribuída. Se a aplicação não tem já instalada no nó (consulte passo #2), pode ser transferido aqui em vez disso. Quando as transferências estiverem concluídas, as tarefas são executadas no respetivos nós atribuídas.

5. Como executar as tarefas, pode consultar lote para monitorizar o progresso da tarefa e as suas tarefas. Seu serviço ou aplicação cliente comunica com o serviço de lote por HTTPS e, uma vez que podem ser monitorização milhares de tarefas em execução no milhares de nós de cluster, certifique-se de que [consultar o serviço de lote de forma eficaz](batch-efficient-list-queries.md).

6. Como as tarefas concluídas, eles podem carregar os respetivos dados de resultado para armazenamento do Windows Azure. Também pode obter ficheiros diretamente a partir de nós de cluster.

7. Quando a monitorização Deteta que as tarefas no seu trabalho estar concluído, o seu serviço ou aplicação cliente pode transferir os dados de saída para processamento suplementar ou avaliação.

Manter em mente trata-se apenas uma forma de utilizar o lote e este cenário descreve apenas algumas das suas funcionalidades disponíveis. Por exemplo, pode executar [várias tarefas em paralelo](batch-parallel-node-tasks.md) em cada nó cluster e pode utilizar [tarefas de preparação e de conclusão da tarefa](batch-job-prep-release.md) para preparar os nós para as tarefas, em seguida, limpar o mais tarde.

## <a name="next-steps"></a>Próximos passos

Agora que tem uma descrição geral do serviço do lote, é altura de procurar forma mais aprofundada para saber como pode utilizá-lo para processar cargas de trabalho paralelas seu cluster-a com um grau elevado.

- Leia a [Descrição geral da funcionalidade do lote para programadores](batch-api-basics.md), informações essenciais para qualquer pessoa preparar a utilização de lote. O artigo contém informações mais detalhadas sobre recursos de serviço do lote como conjuntos de dados, nós, tarefas e tarefas e as funcionalidades de API muitos que pode utilizar durante a criação da sua aplicação lote.

- [Começar a trabalhar com a biblioteca do Azure lote para .NET](batch-dotnet-get-started.md) para saber como utilizar c# e a biblioteca de lote .NET para executar uma carga de trabalho simple com um fluxo de trabalho do lote comuns. Este artigo deve ser um da sua primeira marcas enquanto aprender a utilizar o serviço de lote. Também existe uma [versão Python](batch-python-tutorial.md) do tutorial.

- Transferir os [exemplos de código no GitHub] [ github_samples] para ver como c# tanto Python pode interface com lote para agenda e o processo de exemplo das cargas de trabalho.

- Consulte o artigo o [Caminho de formação do lote] [ learning_path] para obter uma ideia de recursos disponíveis para si como saber funcionar com o lote.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png
