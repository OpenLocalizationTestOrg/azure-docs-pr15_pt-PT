<properties
   pageTitle="Introdução ao Azure lote clip | Microsoft Azure"
   description="Obter uma breve introdução aos comandos do lote no Azure clip para gerir os recursos de serviço do Azure Batch"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="09/30/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-cli"></a>Introdução ao Azure lote clip

A Interface de comandos do Azure em diferentes plataformas (Azure CLI) permite-lhe gerir as suas contas de lote e recursos, tais como conjuntos de dados, tarefas e tarefas no conchas de comando Linux, Mac e Windows. Com o clip Azure, pode executar e muitas das mesmas tarefas que realizar com o lote APIs, Azure portal e os cmdlets do PowerShell lote de script.

Este artigo é baseado no Azure clip versão 0.10.5.

## <a name="prerequisites"></a>Pré-requisitos

* [Instalar o clip Azure](../xplat-cli-install.md)

* [Ligar o clip do Azure à sua subscrição do Azure](../xplat-cli-connect.md)

* Mudar para o **modo de Gestor de recursos**:`azure config mode arm`

>[AZURE.TIP] Recomendamos que atualizar a instalação do Azure clip com frequência para tirar partido das atualizações de serviço e os melhoramentos.

## <a name="command-help"></a>Ajuda de comandos

Pode apresentar texto de ajuda para cada comando no clip do Azure ao acrescentá `-h` como a única opção após o comando. Por exemplo:

* Para obter ajuda sobre o `azure` comando, introduza:`azure -h`
* Para obter uma lista de todos os comandos do lote no clip, utilize:`azure batch -h`
* Para obter ajuda sobre a criação de uma conta do lote, introduza:`azure batch account create -h`

Se estiver na dúvida, utilize o `-h` opção linha de comandos para obter ajuda sobre qualquer comando clip Azure.

## <a name="create-a-batch-account"></a>Criar uma conta do lote

Utilização:

    azure batch account create [options] <name>

Exemplo:

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

Cria uma nova conta do lote com os parâmetros especificados. Tem de especificar, pelo menos, uma localização, o grupo de recursos e o nome da conta. Se ainda não tiver um grupo de recursos, crie uma executando `azure group create`e especifique uma das regiões Azure (tal como "oeste-nos") para o `--location` opção. Por exemplo:

    azure group create --name "resgroup001" --location "West US"

> [AZURE.NOTE] O nome da conta lote tem de ser exclusivo dentro de uma região Azure que a conta estiver criada. Pode conter apenas caracteres alfanuméricos minúsculas e tem de ser 3-24 carateres de comprimento. Não é possível utilizar carateres especiais como `-` ou `_` em nomes de contas lote.

### <a name="linked-storage-account-autostorage"></a>Conta de armazenamento ligadas (autostorage)

Pode ligar (opcionalmente) uma conta de armazenamento **Geral finalidade** à sua conta do lote quando o criar. A funcionalidade de [pacotes de aplicações](batch-application-packages.md) do lote utiliza armazenamento de BLOBs num objetivo geral ligado conta de armazenamento, tal como é que a biblioteca de [Lote ficheiro convenções .NET](batch-task-output.md) . Estas funcionalidades opcionais ajudam a implementar aplicações que executar as tarefas de lote e persistência os dados que produzam.

Para associar uma conta de armazenamento do Windows Azure existente a uma nova conta do lote quando o criar, especifique o `--autostorage-account-id` opção. Esta opção requer o ID do recurso completamente qualificado da conta de armazenamento.

Em primeiro lugar, mostre detalhes da sua conta de armazenamento:

    azure storage account show --resource-group "resgroup001" "storageaccount001"

Em seguida, utilize o valor de **Url** para o `--autostorage-account-id` opção. O valor de Url começa por "/ subscrições /" e contém o caminho de ID e recursos de subscrição para a conta de armazenamento:

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## <a name="delete-a-batch-account"></a>Eliminar uma conta do lote

Utilização:

    azure batch account delete [options] <name>

Exemplo:

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

Elimina a conta lote especificada. Quando lhe for pedido, confirme que pretende remover a conta (remoção da conta pode demorar algum tempo para concluir).

## <a name="manage-account-access-keys"></a>Gerir as teclas de acesso de conta

Precisa de uma tecla de acesso para [criar e modificar recursos](#create-and-modify-batch-resources) na sua conta do lote.

### <a name="list-access-keys"></a>Teclas de acesso de lista

Utilização:

    azure batch account keys list [options] <name>

Exemplo:

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

Lista as teclas de conta para a conta do lote determinada.

### <a name="generate-a-new-access-key"></a>Gerar uma nova chave de acesso

Utilização:

    azure batch account keys renew [options] --<primary|secondary> <name>

Exemplo:

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

Gera novamente a chave de conta especificada para a conta lote especificada.

## <a name="create-and-modify-batch-resources"></a>Criar e modificar lote recursos

Pode utilizar o clip do Azure para criar, ler, atualizar e eliminar (CRUD) lote recursos como conjuntos de dados, calcular nós, tarefas e tarefas. Estas operações CRUD requerem o seu nome de conta do lote, tecla de acesso e ponto final. Pode especificá-los com o `-a`, `-k`, e `-u` opções ou conjunto de [variáveis de ambiente](#credential-environment-variables) de que o clip utiliza automaticamente (se povoada).

### <a name="credential-environment-variables"></a>Variáveis de ambiente de credenciais

Pode definir `AZURE_BATCH_ACCOUNT`, `AZURE_BATCH_ACCESS_KEY`, e `AZURE_BATCH_ENDPOINT` variáveis de ambiente em vez de especificando `-a`, `-k`, e `-u` opções na linha de comandos para cada comando Executar. O clip de lote utiliza estes variáveis (se definir) para que pode omitir o `-a`, `-k`, e `-u` opções. O restante deste artigo assume utilização destas variáveis ambiente.

>[AZURE.TIP] Listar as suas chaves com `azure batch account keys list`e apresentar o ponto final da conta com `azure batch account show`.

### <a name="json-files"></a>Ficheiros JSON

Quando cria recursos lote como conjuntos de dados e tarefas, pode especificar um ficheiro JSON que contenha a configuração do novo recurso em vez de prisma respetivos parâmetros da linha de comandos como opções. Por exemplo:

`azure batch pool create my_batch_pool.json`

Enquanto possa efetuar muitas operações de criação de recursos utilizando apenas as opções da linha de comandos, algumas funcionalidades necessitam de um ficheiro formatados como JSON que contém os detalhes do recurso. Por exemplo, tem de utilizar um ficheiro JSON se pretender especificar ficheiros de recursos para uma tarefa de início.

Para localizar JSON necessária para criar um recurso, consulte a [referência de lote REST API] [ rest_api] documentação no MSDN. Cada tópico "Adicionar *tipo de recurso"*contém exemplo JSON para criar o recurso, que pode utilizar como modelos para os seus ficheiros JSON. Por exemplo, pode encontrar JSON para a criação de conjunto de dados em [Adicionar um agrupamento de uma conta][rest_add_pool].

>[AZURE.NOTE] Se especificar um ficheiro JSON quando cria um recurso, todos os outros parâmetros que especificou na linha de comandos para esse recurso são ignorados.

## <a name="create-a-pool"></a>Criar um conjunto de dados

Utilização:

    azure batch pool create [options] [json-file]

Exemplo (Máquina Virtual configuração):

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

Exemplo (configuração de serviços na nuvem):

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

Cria um conjunto de nós de cluster no serviço lote.

Conforme mencionado na [Descrição geral de funcionalidade do lote](batch-api-basics.md#pool), tem duas opções quando seleciona um sistema operativo para os nós no seu conjunto: **Configuração da Máquina Virtual** e **Configuração dos serviços de nuvem**. Utilizar o `--image-*` opções para criar conjuntos de dados de configuração de Máquina Virtual, e `--os-family` para criar conjuntos de configuração de serviços na nuvem. Não é possível especificar ambos `--os-family` e `--image-*` opções.

Pode especificar agrupamento [pacotes de aplicações](batch-application-packages.md) e a linha de comandos para uma [tarefa de iniciar](batch-api-basics.md#start-task). Para especificar os ficheiros de recursos para a tarefa de início, no entanto, tem em vez de utilizar um [ficheiro JSON](#json-files).

Elimine um conjunto de com:

    azure batch pool delete [pool-id]

>[AZURE.TIP] Verifique a [lista de imagens de máquina virtual](batch-linux-nodes.md#list-of-virtual-machine-images) para valores adequada para a `--image-*` opções.

## <a name="create-a-job"></a>Criar uma tarefa

Utilização:

    azure batch job create [options] [json-file]

Exemplo:

    azure batch job create --id "job001" --pool-id "pool001"

Adiciona uma tarefa para a conta do lote e especifica o conjunto de dados no qual executar suas tarefas.

Elimine uma tarefa com:

    azure batch job delete [job-id]

## <a name="list-pools-jobs-tasks-and-other-resources"></a>Conjuntos de dados de lista, tarefas, tarefas e outros recursos

Cada tipo de recurso lote suporta um `list` comando que consultas a sua conta do lote e listas de recursos desse tipo. Por exemplo, pode listar os conjuntos de na sua conta e as tarefas numa tarefa:

    azure batch pool list
    azure batch task list --job-id "job001"

### <a name="listing-resources-efficiently"></a>Listar recursos de forma eficaz

Para consultar mais rápido, pode especificar **Selecione**, **Filtrar**e **Expandir** opções cláusula para `list` operações. Utilize estas opções para limitar a quantidade de dados devolvidos pelo serviço lote. Uma vez que todas as filtragens de ocorre lado do servidor, apenas os dados que lhe interessam cruza o fio. Utilize estes cláusulas para guardar a largura de banda (e, consequentemente, tempo) quando executar operações de lista.

Por exemplo, esta devolverá apenas conjuntos de dados cujos ids começam com "renderTask":

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

O clip de lote suporta todos os três cláusulas: suportado pelo serviço do lote:

* `--select-clause [select-clause]`Devolver um subconjunto de propriedades para cada entidade
* `--filter-clause [filter-clause]`Devolver apenas entidades que correspondem a expressão OData especificada
* `--expand-clause [expand-clause]`Obter as informações de entidade numa única chamada resto subjacente. A cláusula expandir suporta apenas a `stats` propriedade neste momento.

Para obter detalhes sobre a três cláusulas: e executar consultas de lista com os mesmos, consulte o artigo [consultar o serviço de Azure lote de forma eficaz](batch-efficient-list-queries.md).

## <a name="application-package-management"></a>Gestão de pacote de aplicações

Pacotes de aplicações fornecem uma maneira simplificada de implementação de aplicações para os nós de cluster no seu conjuntos de dados. Com o clip Azure, pode carregar pacotes de aplicações, gerir versões de pacote e eliminar pacotes.

Para criar uma nova aplicação e adicionar uma versão de pacote:

**Criar** uma aplicação:

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**Adicionar** um pacote de aplicações:

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**Ativar** o pacote de:

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

Defina a **versão predefinida** para a aplicação:

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### <a name="deploy-an-application-package"></a>Implementar um pacote de aplicações

Pode especificar um ou mais pacotes de aplicação para implementação quando cria um novo conjunto de dados. Quando especificar um pacote na hora de criação de conjunto de dados, é implementada para cada nó como o conjunto de associações nó. Pacotes também são implementados quando um nó é reiniciado ou reimaged.

Especificar o `--app-package-ref` opção ao criar um agrupamento para implementar um pacote de aplicações nós do conjunto que participar o agrupamento. O `--app-package-ref` opção aceita uma lista delimitado por ponto e vírgula de ids de aplicação para implementar os nós de cluster de.

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

Quando cria um conjunto de dados, utilizando as opções da linha de comandos, atualmente não pode especificar *que* versão do pacote de aplicação para implementar os nós de cluster, por exemplo "1.10-beta3". Por isso, tem de especificar primeiro uma versão predefinida para a aplicação com `azure batch application set [options] --default-version <version-id>` antes de criar o conjunto de (consulte a secção anterior). No entanto, pode especificar uma versão do pacote para o conjunto de se utilizar um [ficheiro JSON](#json-files) em vez de opções de linha de comandos quando criar o conjunto de dados.

Pode encontrar mais informações sobre pacotes de aplicações na [implementação de aplicações com o Azure lote pacotes de aplicações](batch-application-packages.md).

>[AZURE.IMPORTANT] [Ligação de uma conta de armazenamento do Windows Azure](#linked-storage-account-autostorage) , tem à sua conta do lote para utilizar os pacotes de aplicações.

### <a name="update-a-pools-application-packages"></a>Pacotes de aplicação de um conjunto de actualização

Para atualizar as aplicações atribuídas a um conjunto existente, emitir o `azure batch pool set` comando com o `--app-package-ref` opção:

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

Para implementar o novo pacote de aplicação para calcular nós já num conjunto de dados existente, tem de reiniciar o ou reimage em nós:

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

>[AZURE.TIP] Pode obter uma lista de nós num conjunto de dados, juntamente com os ids de nó com `azure batch node list`.

Tenha em atenção que tem já configurou a aplicação com uma versão predefinida antes de implementação (`azure batch application set [options] --default-version <version-id>`).

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

Esta secção destina-se para fornecer-lhe recursos para utilizar quando resolução de problemas de clip Azure. -Não seja necessariamente todos os resolver problemas relacionados, mas poderão ajudá-lo limitar a causa e aponte para recursos de ajuda.

* Utilizar `-h` para ver o **texto de ajuda** para a qualquer comando clip

* Utilizar `-v` e `-vv` para apresentar o resultado de comando **verboso** ; `-vv` é "extra" verboso e apresenta o resto pedidos e respostas real. Estes parâmetros são úteis para apresentar o erro completo de saída.

* Pode ver o **resultado do comando como JSON** com o `--json` opção. Por exemplo, `azure batch pool show "pool001" --json` mostra as propriedades do pool001 no formato JSON. Pode copiar e modificar este resultado para utilizar um `--json-file` (consulte o artigo [ficheiros JSON](#json-files) neste artigo).

* [Fórum do lote no MSDN] [ batch_forum] é um recurso de ajuda excelente e é monitorizada estreitamente pelos membros da equipa lote. Certifique-se de que publique aí as suas perguntas caso se depare com problemas ou gostaria de obter ajuda com uma operação específica.

* Operação do recurso não cada lote atualmente é suportada o clip do Azure. Por exemplo, atualmente não é possível especificar um pacote *versão* da aplicação de um conjunto, apenas o ID do pacote. Nestes casos, poderá ter de fornecer um `--json-file` para o seu comando em vez de utilizar as opções da linha de comandos. Certifique-se de que se manter atualizado com a versão mais recente do clip para Pegue melhoramentos futuros.

## <a name="next-steps"></a>Próximos passos

*  Consulte o artigo [implementação da aplicação com pacotes de aplicações do Azure lote](batch-application-packages.md) para saber como utilizar esta funcionalidade para gerir e implementar aplicações que executar em nós de cluster lote.

* Consulte [consultar o serviço de lote de forma eficaz](batch-efficient-list-queries.md) para mais como reduzir o número de itens e o tipo de informações que são devolvidos para as consultas ao lote.

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx