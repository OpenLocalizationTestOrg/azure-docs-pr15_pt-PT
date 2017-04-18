<properties
    pageTitle="Nós Linux agrupamentos de Azure lote | Microsoft Azure"
    description="Saiba como a processar das cargas de trabalho seu cluster paralelo no agrupamentos de máquinas virtuais de Linux no lote Azure."
    services="batch"
    documentationCenter="python"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="marsma" />

# <a name="provision-linux-compute-nodes-in-azure-batch-pools"></a>Aprovisionar Linux cluster nós agrupamentos de lote do Azure

Pode utilizar o Azure lote para executar cluster paralelas cargas de trabalho em máquinas virtuais de Linux e Windows. Este artigo explica como criar conjuntos de dados de nós do cluster Linux no serviço lote utilizando o [Lote Python] de[ py_batch_package] e [Lote .NET] [ api_net] bibliotecas do cliente.

> [AZURE.NOTE] [Pacotes de aplicações](batch-application-packages.md) atualmente não são suportadas em nós de cluster Linux.

## <a name="virtual-machine-configuration"></a>Configuração de máquina virtual

Quando cria um conjunto de nós de cluster no lote, tem duas opções a partir do qual selecionar o tamanho de nó e o sistema operativo: configuração dos serviços de nuvem e configuração de Máquina Virtual.

**Configuração dos serviços de nuvem** fornece que Windows calcular nós *apenas*. Tamanhos de nó cluster disponíveis estão listados na [tamanhos dos serviços em nuvem](../cloud-services/cloud-services-sizes-specs.md), e sistemas operativos disponíveis estão listados na [Azure convidado SO lançamentos e da matriz de compatibilidade SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Quando cria um agrupamento que contém nós de serviços em nuvem Azure, tem de especificar apenas o tamanho de nó e respetiva "da família SO," quais são descritos nos artigos mencionados anteriormente. Para conjuntos de dados do Windows calcular nós, Cloud Services é utilizado frequentemente.

**Configuração de máquina virtual** fornece imagens Linux e Windows para cluster nós. Tamanhos de nó cluster disponíveis estão listados na [tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) e [tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Quando cria um agrupamento que contém nós de configuração de Máquina Virtual, tem de especificar o tamanho dos nós, a referência de imagem de máquina virtual e o agente de nó lote SKU de ser instalada nos nós.

### <a name="virtual-machine-image-reference"></a>Referência de imagem de máquina virtual

O serviço de lote utiliza a [escala de Máquina Virtual define](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para fornecer nós de cluster Linux. As imagens de sistema operativo para estes máquinas virtuais são fornecidas pelo [Azure Marketplace][vm_marketplace]. Quando configura uma referência de imagem de máquina virtual, especifique as propriedades de uma imagem de máquina virtual Marketplace. As seguintes propriedades são necessárias ao criar uma referência de imagem de máquina virtual:

| **Propriedades de referência da imagem** | **Exemplo** |
| ----------------- | ------------------------ |
| Publisher         | Canónico                |
| Oferta             | UbuntuServer             |
| SKU               | 14.04.4-LTS              |
| Versão           | mais recente                   |

> [AZURE.TIP] Pode obter mais informações sobre estas propriedades e como listar Marketplace imagens no [navegar e selecionadas imagens de máquina virtual Linux no Azure com clip ou PowerShell](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md). Note que estão atualmente compatíveis com o lote nem todas as imagens de Marketplace. Para mais informações, consulte o artigo [agente de nó SKU](#node-agent-sku).

### <a name="node-agent-sku"></a>Agente de nó SKU

O agente de nó lote é um programa que é executado em cada nó no conjunto e fornece a interface de comandos e controlo entre o nó e o serviço do lote. Existem diversas implementações do agente de nó, conhecida como SKUs, para sistemas operativos diferentes. Basicamente, quando cria uma configuração de Máquina Virtual, primeiro de especificar a referência de imagem de máquina virtual e, em seguida, especificar o agente de nó instalar na imagem. Normalmente, cada agente de nó, SKU é compatível com múltiplas imagens de máquina virtual. Eis alguns exemplos de agente de nó SKUs:

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.Windows amd64

> [AZURE.IMPORTANT] Nem todas as imagens de máquina virtual que estão disponíveis no mercado são compatíveis com os agentes de nó lote encontra-se disponível. Tem de utilizar o SDK do lote para listar o agente de nó disponível SKUs e as imagens de máquina virtual com o qual são compatíveis. Ver a [lista de Máquina Virtual imagens](#list-of-virtual-machine-images) neste artigo para obter mais informações.

## <a name="create-a-linux-pool-batch-python"></a>Criar um conjunto de Linux: Batch Python

O fragmento de código seguinte mostra um exemplo de como utilizar a [Biblioteca de cliente do Microsoft Azure lote para Python] [ py_batch_package] para criar um conjunto de servidor de Ubuntu nós de cluster. Documentação de referência para o módulo Python lote pode ser encontrada em [azure.batch pacote] [py_batch_docs] no leitura a documentos.

Este fragmento cria uma [ImageReference] [ py_imagereference] explicitamente e especifica cada uma das suas propriedades (publisher, versão de oferta, SKU,). No código de produção, no entanto, recomendamos que utilize o [list_node_agent_skus] [ py_list_skus] método para determinar e selecione as disponíveis imagem nó agente SKU combinações e o tempo de execução.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Tal como mencionado anteriormente, recomendamos que em vez de criar o [ImageReference] [ py_imagereference] explicitamente, utiliza o [list_node_agent_skus] [ py_list_skus] método para selecionar dinamicamente a partir das combinações de imagem do agente/Marketplace nó atualmente suportados. O fragmento de Python seguinte mostra a utilização deste método.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Criar um conjunto de Linux: lote .NET

O fragmento de código seguinte mostra um exemplo de como utilizar o [Lote .NET] [ nuget_batch_net] biblioteca do cliente para criar um conjunto de servidor de Ubuntu nós de cluster. Pode encontrar a [documentação de referência do lote .NET] [ api_net] no MSDN.

O fragmento de código seguinte utiliza a [PoolOperations][net_pool_ops]. [ListNodeAgentSkus] [net_list_skus] método para selecionar a partir da lista de atualmente suportados Marketplace imagem e nó agente SKU combinações. Esta técnica é conveniente porque a lista de combinações suportadas podem ser alteradas a frequentemente. Mais frequentemente, combinações suportadas são adicionadas.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.SkuId.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicated: nodeCount);

// Commit the pool to the Batch service
pool.Commit();
```

Apesar do fragmento de anterior utiliza o [PoolOperations][net_pool_ops]. [ListNodeAgentSkus] [net_list_skus] método para dinamicamente da lista e selecione a partir do suportado imagem e nó agente SKU combinações (recomendadas), também pode configurar um [ImageReference] [ net_imagereference] explicitamente:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lista de imagens de máquina virtual

A tabela seguinte apresenta as imagens de máquina virtual Marketplace que são compatíveis com os agentes de nó lote disponíveis quando este artigo foram atualizados pela última vez. É importante ter em atenção que esta lista não é definitiva porque imagens e agentes de nó podem ser adicionados ou removidos em qualquer altura. Recomendamos que suas lote aplicações e serviços utilizem sempre [list_node_agent_skus] [ py_list_skus] (Python) e [ListNodeAgentSkus] [ net_list_skus] (lote .NET) para determinar e selecione as SKUs encontra-se disponível.

> [AZURE.WARNING] A seguinte lista podem ser alteradas em qualquer altura. Utilize sempre os métodos de **agente de nó lista SKU** disponíveis no APIs do lote para listar e, em seguida, selecione a partir da compatível máquina virtual e agente de nó SKUs ao executar as tarefas de lote.

| **Publisher** | **Oferta** | **Imagem SKU** | **Versão** | **Agente de nó SKU ID** |
| ------- | ------- | ------- | ------- | ------- |
| Canónico | UbuntuServer | 14.04.0-LTS | mais recente | batch.node.ubuntu 14.04 |
| Canónico | UbuntuServer | 14.04.1-LTS | mais recente | batch.node.ubuntu 14.04 |
| Canónico | UbuntuServer | 14.04.2-LTS | mais recente | batch.node.ubuntu 14.04 |
| Canónico | UbuntuServer | 14.04.3-LTS | mais recente | batch.node.ubuntu 14.04 |
| Canónico | UbuntuServer | 14.04.4-LTS | mais recente | batch.node.ubuntu 14.04 |
| Canónico | UbuntuServer | 14.04.5-LTS | mais recente | batch.node.ubuntu 14.04 |
| Canónico | UbuntuServer | 16.04.0-LTS | mais recente | batch.node.ubuntu 16.04 |
| Credativ | Debian | 8 | mais recente | batch.node.debian 8 |
| OpenLogic | CentOS | 7.0 | mais recente | batch.node.centos 7 |
| OpenLogic | CentOS | 7.1 | mais recente | batch.node.centos 7 |
| OpenLogic | CentOS HPC | 7.1 | mais recente | batch.node.centos 7 |
| OpenLogic | CentOS | 7.2 | mais recente | batch.node.centos 7 |
| Oracle | Oracle Linux | 7.0 | mais recente | batch.node.centos 7 |
| SUSE | openSUSE | 13.2 | mais recente | batch.node.OpenSuSE 13.2 |
| SUSE | openSUSE bissexto | 42.1 | mais recente | batch.node.OpenSuSE 42.1 |
| SUSE | SLES HPC | 12 | mais recente | batch.node.OpenSuSE 42.1 |
| SUSE | SLES | 12-SP1 | mais recente | batch.node.OpenSuSE 42.1 |
| Microsoft anúncios | padrão-dados ciência vm | padrão-dados ciência vm | mais recente | batch.node.Windows amd64 |
| Microsoft anúncios | Linux-dados ciência vm | linuxdsvm | mais recente | batch.node.centos 7 |
| MicrosoftWindowsServer | WindowsServer | 2008 R2-SP1 | mais recente | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | Centro de dados de 2012 | mais recente | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Centro de dados | mais recente | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | Windows Server-Technical Preview | mais recente | batch.node.Windows amd64 |

## <a name="connect-to-linux-nodes"></a>Ligar a Linux nós

Durante o desenvolvimento, ou durante a resolução de problemas, poderá ser necessário iniciar sessão para os nós no seu conjunto. Ao contrário de nós de cluster do Windows, é possível utilizar o protocolo de ambiente de trabalho remoto (RDP) para ligar a nós Linux. Em vez disso, o serviço do lote ativa o acesso SSH em cada nó para a ligação remoto.

O fragmento de código Python seguinte cria um utilizador em cada nó num conjunto de, que é necessário para a ligação remoto. Este imprime, em seguida, as informações de ligação de shell seguro (SSH) para cada nó.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
        credentials,
        base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Eis a saída de exemplo para o código anterior para um conjunto de dados que contém quatro Linux nós:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Tenha em atenção que em vez de uma palavra-passe, pode especificar uma chave pública SSH quando cria um utilizador num nó. No Python SDK, isto é feito ao utilizar o parâmetro **ssh_public_key** no [ComputeNodeUser][py_computenodeuser]. No .NET, isto é feito utilizando o [ComputeNodeUser][net_computenodeuser]. [SshPublicKey] [net_ssh_key] propriedade.

## <a name="pricing"></a>Preços

Azure lote é criada com base em tecnologia serviços em nuvem Azure e máquinas virtuais do Azure. O serviço do lote é oferecido sem custos, o que significa que lhe vai ser cobrada apenas para os recursos de cluster que as soluções do lote consumam. Quando escolhe **Configuração dos serviços de nuvem**, será cobrado com base nos [serviços em nuvem preços] [ cloud_services_pricing] estrutura. Quando escolhe **Máquina Virtual configuração**, será cobrado com base em [máquinas virtuais preços] [ vm_pricing] estrutura.

## <a name="next-steps"></a>Próximos passos

### <a name="batch-python-tutorial"></a>Tutorial do lote Python

Para obter um tutorial de forma mais aprofundado sobre como trabalhar com o lote utilizando Python, consulte o artigo [Introdução ao cliente do Azure lote Python](batch-python-tutorial.md). O acompanha [código de exemplo] [ github_samples_pyclient] inclui uma função de helper `get_vm_config_for_distro`, que mostra técnica de outra para obter uma configuração de máquina virtual.

### <a name="batch-python-code-samples"></a>Exemplos de código do lote Python

Dar saída a outros [Python código amostras] [ github_samples_py] nas [amostras de lote azure] [ github_samples] repositório no GitHub para vários scripts que lhe mostram como efetuar operações lote comuns como conjunto, tarefa e criação de tarefas. [Leia-me] [ github_py_readme] que acompanham o Python amostras tem mais informações sobre como instalar os pacotes necessários.

### <a name="batch-forum"></a>Fórum do lote

[Fórum do Azure lote] [ forum] no MSDN é um ótimo local para discutir lote e perguntas sobre o serviço. Ler mensagens de úteis "stickied" e publique as suas perguntas que possam ocorrer enquanto que cria as soluções do lote.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
