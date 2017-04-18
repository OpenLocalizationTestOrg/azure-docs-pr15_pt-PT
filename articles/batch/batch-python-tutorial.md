<properties
    pageTitle="Tutorial - introdução ao cliente do Azure lote Python | Microsoft Azure"
    description="Obter informações sobre os conceitos básicos do lote do Azure e como desenvolver o serviço de processo com um cenário simple"
    services="batch"
    documentationCenter="python"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/27/2016"
    ms.author="marsma"/>

# <a name="get-started-with-the-azure-batch-python-client"></a>Introdução ao cliente do Azure lote Python

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Conheça os princípios básicos [Azure lote] [ azure_batch] e o [Lote Python] [ py_azure_sdk] cliente como discutimos uma aplicação do lote pequena escrita Python. Olharmos para utilização de scripts de exemplo como duas o serviço de lote para processar uma carga de trabalho paralela em máquinas virtuais de Linux na nuvem e interagem com [Armazenamento do Windows Azure](./../storage/storage-introduction.md) para transição de ficheiro e obtenção. Irá obter um fluxo de trabalho de aplicação comuns do lote e obter uma base compreensão dos componentes principais do lote como tarefas, tarefas, conjuntos de dados e calcular nós.

![Fluxo de trabalho do lote solução (basic)][11]<br/>

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que tem um conhecimento de trabalho de Python e familiaridade com Linux. Também partem do princípio que é possível satisfazem os requisitos de criação de conta que estão especificados abaixo para Azure e lote e armazenamento de serviços.

### <a name="accounts"></a>Contas

- **Conta Azure**: Se ainda não tiver uma subscrição do Azure, [criar uma conta Azure gratuita][azure_free_account].
- **Conta lote**: assim que tiver uma subscrição do Azure, [crie uma conta Azure lote](batch-account-create-portal.md).
- **Conta de armazenamento**: consulte o artigo [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md).

### <a name="code-sample"></a>Exemplo de código

A tutoriais Python [código de exemplo] [ github_article_samples] dos exemplos de código do lote muitos encontra-se nas [amostras de lote azure] [ github_samples] repositório no GitHub. Pode transferir todas as amostras ao clicar em **clonar ou transferência > Transferir ZIP** na home page do repositório ou ao clicar no [azure-lote amostras master.zip] [ github_samples_zip] ligação direta de transferência. Assim que tiver extraídos o conteúdo do ficheiro ZIP, os dois scripts para este tutorial encontram-se na `article_samples` diretório:

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Ambiente Python

Para executar o script de exemplo *python_tutorial_client.py* estação de trabalho local, tem de **interpretação Python** compatível com a versão **2.7** ou **3.3 +**. O script foi testado Linux e Windows.

### <a name="cryptography-dependencies"></a>dependências de criptografia

Tem de instalar as dependências para de [criptografia] [ crypto] biblioteca, uma ferramenta necessária à `azure-batch` e `azure-storage` pacotes Python. Execute um dos seguintes operações adequadas para a sua plataforma ou referenciar a [instalação de criptografia] [ crypto_install] detalhes para obter mais informações:

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`

* CentOS

    `yum update && yum install -y gcc openssl-dev libffi-devel python-devel`

* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`

* Windows

    `pip install cryptography`

>[AZURE.NOTE] Se instalar para Python 3.3 + em Linux, utilize os equivalentes python3 para as dependências de Python. Por exemplo, no Ubuntu:`apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`

### <a name="azure-packages"></a>Pacotes Azure

Em seguida, instale os pacotes **Lote Azure** e Python de **Armazenamento do Windows Azure** . Pode fazê-lo com **pip** e *requirements.txt* encontradas aqui:

`/azure-batch-samples/Python/Batch/requirements.txt`

Seguir **pip** comando para instalar os pacotes lote e armazenamento de problema:

`pip install -r requirements.txt`

Ou, pode instalar o [lote de azure] [ pypi_batch] e [armazenamento do Windows azure] [ pypi_storage] Python pacotes manualmente:

`pip install azure-batch`<br/>
`pip install azure-storage`

> [AZURE.TIP] Poderá ter de prefixo os seus comandos com `sudo` se estiver a utilizar uma conta privilegiada. Por exemplo, `sudo pip install -r requirements.txt`. Para obter mais informações sobre como instalar pacotes Python, consulte o artigo [Instalar pacotes] [ pypi_install] no readthedocs.io.

## <a name="batch-python-tutorial-code-sample"></a>Exemplo de código de iniciação do lote Python

O exemplo de código tutorial lote Python consiste em dois scripts Python e alguns ficheiros de dados.

- **python_tutorial_client.PY**: interage com os serviços de lote e armazenamento executar uma carga de trabalho paralela na cluster nós (máquinas virtuais). O script *python_tutorial_client.py* é executada no seu estação de trabalho local.

- **python_tutorial_task.PY**: O script que é executada no calcular nós no Azure para executar o trabalho real. No exemplo, *python_tutorial_task.py* analisa o texto num ficheiro transferidas a partir de armazenamento do Windows Azure (o ficheiro de entrada). Em seguida, gera um ficheiro de texto (o ficheiro de exportação) que contém uma lista das três palavras superiores que aparecem no ficheiro de entrada. Depois de criar o ficheiro de exportação, *python_tutorial_task.py* carrega o ficheiro de armazenamento do Windows Azure. Isto torna disponível para transferência para o script de cliente em execução no seu estação de trabalho. O script *python_tutorial_task.py* é executado em paralelo em vários nós de cluster no serviço lote.

- **./data/taskdata\*. txt**: estes ficheiros de três texto fornecem a entrada de dados para as tarefas que são executadas sobre os nós de cluster.

O diagrama seguinte ilustra as operações principais que são executadas pelos scripts de cliente e tarefa. Este fluxo de trabalho básico é típico de muitas soluções de cluster que são criadas com o lote. Enquanto não demonstra todas as funcionalidades disponíveis no serviço do lote, quase todas as cenário lote inclui partes deste fluxo de trabalho.

![Fluxo de trabalho do lote exemplo][8]<br/>

[**Passo 1.**](#step-1-create-storage-containers) Crie **contentores** no armazenamento de Blobs do Azure.<br/>
[**Passo 2.**](#step-2-upload-task-script-and-data-files) Carregar ficheiros de script e a entrada de tarefa para contentores.<br/>
[**Passo 3.**](#step-3-create-batch-pool) Crie um lote de **agrupamento**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3A.** O conjunto de **tarefa de início** transfere o script de tarefa (python_tutorial_task.py) para nós à medida que participar o agrupamento.<br/>
[**Passo 4.**](#step-4-create-batch-job) Crie uma **tarefa**.<br/>
[**Passo 5.**](#step-5-add-tasks-to-job) Adicione **tarefas** à tarefa.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5º.** As tarefas são agendadas executar em nós.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5. oB.** Cada tarefa os respetivos dados de entrada as transferências do armazenamento do Windows Azure, em seguida, começa a execução.<br/>
[**Passo 6.**](#step-6-monitor-tasks) Monitorizar as tarefas.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Como as tarefas sejam concluídas, carregue os respetivos dados de saída ao armazenamento do Azure.<br/>
[**Passo 7.**](#step-7-download-task-output) Transfira a saída de tarefas a partir do armazenamento.

Tal como mencionada não cada lote solução executa estes passos exatos e pode incluir muitos exemplo mais, mas este demonstra processos comuns que se encontram numa solução lote.

## <a name="prepare-client-script"></a>Preparar o script do cliente

Antes de executar o exemplo, adicione as credenciais da conta e o lote de armazenamento para *python_tutorial_client.py*. Se ainda não o tiver o fez, abra o ficheiro no seu editor favorito e Atualize as seguintes linhas com as suas credenciais.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
batch_account_name = "";
batch_account_key  = "";
batch_account_url  = "";

# Storage account credentials
storage_account_name = "";
storage_account_key  = "";
```

Pode encontrar as credenciais da conta lote e armazenamento dentro pá a conta de cada serviço no [portal do Azure][azure_portal]:

![Batch credenciais no portal do][9]
![credenciais de armazenamento no portal][10]<br/>

Nas secções seguintes, vamos analisar os passos utilizados pelos scripts para processar uma carga de trabalho no serviço lote. Recomendamos que se refira regularmente os scripts no seu editor enquanto trabalha básicas o resto do artigo.

Navegue para a linha seguinte num **python_tutorial_client.py** para iniciar com o passo 1:

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>Passo 1: Criar contentores de armazenamento

![Criar contentores no armazenamento do Windows Azure][1]
<br/>

Lote inclui suporte incorporado para interagir com armazenamento do Windows Azure. Contentores na sua conta de armazenamento irão fornecer os ficheiros necessários pelas tarefas que são executados na sua conta do lote. Os contentores também fornecem um local para armazenar os dados de saída produzem as tarefas. A primeira coisa que é que o script *python_tutorial_client.py* é criar três contentores no [Armazenamento de Blobs do Azure](../storage/storage-introduction.md#blob-storage):

- **aplicação**: neste contentor irá guardar o script Python executar pelas tarefas, *python_tutorial_task.py*.
- **Introdução**: tarefas irão transferir os ficheiros de dados para processar desde o contentor de *entrada* .
- **resultado**: Quando tarefas concluir o processamento de ficheiro de entrada, estes serão carregados os resultados ao contentor de *saída* .

Para interagir com uma conta de armazenamento e a criação de contentores, utilizamos o [armazenamento do Windows azure] [ pypi_storage] pacote para criar um [BlockBlobService] [ py_blockblobservice] objeto – "cliente blob". Em seguida, criamos três contentores na conta de armazenamento a utilizar o cliente de Blobs.

```python
 # Create the blob client, for use in obtaining references to
 # blob storage containers and uploading files to containers.
 blob_client = azureblob.BlockBlobService(
     account_name=_STORAGE_ACCOUNT_NAME,
     account_key=_STORAGE_ACCOUNT_KEY)

 # Use the blob client to create the containers in Azure Storage if they
 # don't yet exist.
 app_container_name = 'application'
 input_container_name = 'input'
 output_container_name = 'output'
 blob_client.create_container(app_container_name, fail_on_exist=False)
 blob_client.create_container(input_container_name, fail_on_exist=False)
 blob_client.create_container(output_container_name, fail_on_exist=False)
```

Depois de tem sido criados os contentores, a aplicação agora pode carregar os ficheiros que serão utilizados pelas tarefas.

> [AZURE.TIP] [Como utilizar o armazenamento de Blobs do Azure a partir do Python](../storage/storage-python-how-to-use-blob-storage.md) fornece uma boa visão geral de trabalhar com contentores de armazenamento do Windows Azure e blobs. Deve ser na parte superior da sua lista de leitura como começar a trabalhar com lote.

## <a name="step-2-upload-task-script-and-data-files"></a>Passo 2: Carregar uma tarefa script e ficheiros de dados

![Carregar a aplicação de tarefa e ficheiros de entrada (dados) para contentores][2]
<br/>

Na operação de carregamento de ficheiro, *python_tutorial_client.py* pela primeira vez define coleções de caminhos de ficheiro de **entrada** e de **aplicação** à medida que existam no computador local. Em seguida, serão carregadas estes ficheiros para os contentores que criou no passo anterior.

```python
 # Paths to the task script. This script will be executed by the tasks that
 # run on the compute nodes.
 application_file_paths = [os.path.realpath('python_tutorial_task.py')]

 # The collection of data files that are to be processed by the tasks.
 input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                     os.path.realpath('./data/taskdata2.txt'),
                     os.path.realpath('./data/taskdata3.txt')]

 # Upload the application script to Azure Storage. This is the script that
 # will process the data files, and is executed by each of the tasks on the
 # compute nodes.
 application_files = [
     upload_file_to_container(blob_client, app_container_name, file_path)
     for file_path in application_file_paths]

 # Upload the data files. This is the data that will be processed by each of
 # the tasks executed on the compute nodes in the pool.
 input_files = [
     upload_file_to_container(blob_client, input_container_name, file_path)
     for file_path in input_file_paths]
```

Utilizar a compreensão de lista, a `upload_file_to_container` função chama-se para cada ficheiro nas coleções de ficheiros e duas [ResourceFile] [ py_resource_file] são propagadas coleções de sites. O `upload_file_to_container` função é apresentado abaixo:

```
def upload_file_to_container(block_blob_client, container_name, file_path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """
    blob_name = os.path.basename(file_path)

    print('Uploading file {} to container [{}]...'.format(file_path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles

Um [ResourceFile] [ py_resource_file] fornece tarefas no lote com o URL para um ficheiro no armazenamento do Windows Azure que é transferido para o nó cluster antes dessa tarefa é executada. O [ResourceFile][py_resource_file]. propriedade de **blob_source** Especifica o URL completo do ficheiro, tal como se encontra no armazenamento do Windows Azure. O URL também pode incluir uma assinatura de acesso partilhado (SA) que proporciona acesso seguro para o ficheiro. A maioria dos tipos de tarefas no lote incluem uma propriedade de *ResourceFiles* , incluindo:

- [CloudTask][py_task]
- [Tarefa de início][py_starttask]
- [JobPreparationTask][py_jobpreptask]
- [JobReleaseTask][py_jobreltask]

Este exemplo não utiliza os tipos de tarefas JobPreparationTask ou JobReleaseTask, mas pode ler mais sobre os mesmos no [Executar preparação e de conclusão de tarefas de projecto no Azure lote calcular nós](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Assinatura de acesso partilhado (SA)

Acesso partilhado assinaturas são cadeias que fornecem acesso seguro a contentores e blobs no armazenamento do Windows Azure. O script *python_tutorial_client.py* utiliza ambos os blob e contentor partilhados assinaturas de acesso e demonstra como obter estas cadeias de assinatura acesso partilhado a partir do serviço de armazenamento.

- **Blob partilhado acesso assinaturas**: utilizações de tarefa de início do conjunto de assinaturas acesso partilhado blob quando transfere os ficheiros de dados de script e entrada da tarefa a partir do armazenamento (consulte [passo #3](#step-3-create-batch-pool) ). O `upload_file_to_container` função no *python_tutorial_client.py* contém o código que obtém a assinatura de acesso partilhado cada blob. Fizer ao contactar o suporte [BlockBlobService.make_blob_url] [ py_make_blob_url] no módulo armazenamento.

- **Assinatura de acesso partilhado contentor**: como cada tarefa termina o seu trabalho no nó cluster, carrega o ficheiro de exportação para o contentor de *saída* no armazenamento do Windows Azure. Para fazê-lo, *python_tutorial_task.py* utiliza uma assinatura de acesso partilhado contentor que fornece acesso de escrita para o contentor. O `get_container_sas_token` função no *python_tutorial_client.py* obtém a assinatura de acesso partilhado o contentor, o que é passada, em seguida, como um argumento da linha de comandos para as tarefas. Passo #5, [Adicionar tarefas a uma tarefa](#step-5-add-tasks-to-job), aborda a utilização do contentor SA.

> [AZURE.TIP] Dar saída a série de duas partes sobre assinaturas de acesso partilhado, [parte 1: Noções sobre o modelo de SA](../storage/storage-dotnet-shared-access-signature-part-1.md) e [parte 2: criar e utilizar uma SA com o serviço de BLOBs](../storage/storage-dotnet-shared-access-signature-part-2.md), para saber mais sobre como fornecer acesso seguro a dados na sua conta de armazenamento.

## <a name="step-3-create-batch-pool"></a>Passo 3: Criar o conjunto de dados Batch

![Criar um conjunto de comandos][3]
<br/>

Um lote de **agrupamento** é uma coleção de cluster nós (máquinas virtuais) no qual o lote executa tarefas de uma tarefa.

Depois de-os carregamentos pendentes os tarefa script e ficheiros de dados para a conta de armazenamento, *python_tutorial_client.py* inicia respetiva interação com o serviço do lote utilizando o módulo lote Python. Para fazê-lo, um [BatchServiceClient] [ py_batchserviceclient] é criado:

```python
 # Create a Batch service client. We'll now be interacting with the Batch
 # service in addition to Storage.
 credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                              _BATCH_ACCOUNT_KEY)

 batch_client = batch.BatchServiceClient(
     credentials,
     base_url=_BATCH_ACCOUNT_URL)
```

Em seguida, é criado um conjunto de nós de cluster na conta lote com uma chamada para `create_pool`.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Quando cria um conjunto de dados, pode definir um [PoolAddParameter] [ py_pooladdparam] que especifica várias propriedades para o conjunto de:

- **ID** do agrupamento de (*id* - necessário)<p/>Tal como acontece com a maior parte das entidades no lote, o seu novo conjunto de dados tem de ter um ID exclusivo dentro da sua conta do lote. Refere-se do seu código para este conjunto de dados com o seu ID e é como identificar o agrupamento no Azure [portal][azure_portal].

- **Número de nós de cluster** (*target_dedicated* - necessário)<p/>Esta propriedade especifica VMs quantos devem ser implementadas no conjunto. É importante ter em atenção que todas as contas do lote têm uma predefinida **quota** limita o número de **núcleos** (e, consequentemente, nós de cluster) numa conta lote. Pode encontrar as quotas de predefinidas e instruções sobre como para [aumentar uma quota](batch-quota-limit.md#increase-a-quota) (como o número máximo de tarolos na sua conta do lote) na [Quotas e os limites para o serviço do Azure lote](batch-quota-limit.md). Se encontrar o seu próprio perguntar "Por que motivo não meu agrupamento alcançar mais de X nós?" Esta quota core pode ser a causa.

- **Sistema operativo** para nós (*virtual_machine_configuration* **ou** *cloud_service_configuration* - necessário)<p/>No *python_tutorial_client.py*, recomendamos criar um conjunto de nós de Linux utilizando um [VirtualMachineConfiguration][py_vm_config]. O `select_latest_verified_vm_image_with_node_agent_sku` funcionam em `common.helpers` simplifica a trabalhar com o [Azure Marketplace máquinas virtuais] [ vm_marketplace] imagens. Consulte o artigo [Linux disposição calcular nós no Azure lote conjuntos de dados](batch-linux-nodes.md) para obter mais informações sobre como utilizar imagens Marketplace.

- **Tamanho de nós do cluster** (*vm_size* - necessário)<p/>Uma vez que vamos especificar Linux nós para os nossos [VirtualMachineConfiguration][py_vm_config], vamos especificar um tamanho da memória virtual (`STANDARD_A1` neste exemplo) a partir de [tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-linux-sizes.md). Novamente, consulte o artigo [aprovisionar Linux calcular nós no Azure lote conjuntos de dados](batch-linux-nodes.md) para obter mais informações.

- **Iniciar a tarefa** (*start_task* - não é necessário)<p/>Juntamente com o acima propriedades nó físico, também pode especificar uma [tarefa de início] [ py_starttask] para o conjunto de dados (não é necessário). A tarefa de início executa em cada nó à medida que o nó agrupa o agrupamento e, sempre que um nó é reiniciado. A tarefa de início é especialmente útil para preparar nós de cluster para a execução das tarefas, tais como instalar as aplicações que são executadas as suas tarefas.<p/>Na aplicação neste exemplo, a tarefa de início copia os ficheiros que transfere a partir do armazenamento (que são especificadas utilizando **resource_files** propriedade a tarefa de início) a tarefa de início de *directório de trabalho* ao directório *partilhado* que podem aceder a todas as tarefas em execução no nó. Basicamente, desta forma, copia `python_tutorial_task.py` ao directório partilhado em cada nó tal como o nó adere o conjunto de, para que todas as tarefas que execute no nó possam aceder à mesma.

É possível que existam a chamada para o `wrap_commands_in_shell` função helper. Esta função leva-o até um conjunto de comandos separados e cria uma única linha de comandos adequado para a propriedade de linha de comandos de uma tarefa.

Também notáveis no fragmento de código acima são a utilização de duas variáveis de ambiente a propriedade de **linha de comandos** da tarefa de início: `AZ_BATCH_TASK_WORKING_DIR` e `AZ_BATCH_NODE_SHARED_DIR`. Cada nó cluster dentro de um conjunto de lote é configurado automaticamente com várias variáveis de ambiente que sejam específicas lote. Qualquer processo que é executado por uma tarefa tem acesso a estas variáveis de ambiente.

> [AZURE.TIP] Para saber mais sobre o ambiente de variáveis que estão disponíveis em nós de cluster de um conjunto de lote, bem como obter informações sobre como directórios de trabalho da tarefa, consulte o artigo **definições de ambiente de tarefas** e **ficheiros e directórios** na [Descrição geral das funcionalidades do Azure lote](batch-api-basics.md).

## <a name="step-4-create-batch-job"></a>Passo 4: Criar rotina

![Criar rotina][4]<br/>

Uma **tarefa** é um conjunto de tarefas e está associado um conjunto de nós de cluster. Executar as tarefas numa tarefa em nós de cluster o agrupamento associado.

Pode utilizar uma tarefa não só para organizar e controlar tarefas relacionadas das cargas de trabalho, mas também para impor determinadas restrições – tal como o runtime máxima para a tarefa (e por extensão, as suas tarefas) e prioridade em relação a outras tarefas na conta do lote de cargo. Neste exemplo, no entanto, a tarefa está associada apenas com o conjunto de que foi criado no passo #3. Sem propriedades adicionais são configuradas.

Todas as tarefas de lote estão associadas um conjunto específico. Esta associação indica quais os nós executar tarefas de projecto no. Especificar o agrupamento ao utilizar o [PoolInformation] [ py_poolinfo] propriedade, conforme apresentado no fragmento de código abaixo.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Agora que uma tarefa foi criada, tarefas são adicionadas ao efetuar o trabalho.

## <a name="step-5-add-tasks-to-job"></a>Passo 5: Adicionar tarefas à tarefa

![Adicionar tarefas à tarefa][5]<br/>
*(1) tarefas são adicionadas à tarefa, (2) as tarefas são agendadas para ser executada em nós e (3) as tarefas transferir os ficheiros de dados para processar*

Lote as **tarefas** são as unidades individuais de trabalho que são executados nos nós de cluster. Uma tarefa tem uma linha de comandos e executa os scripts ou executáveis que especificar nessa linha de comandos.

Para executar realmente trabalho, tarefas tem de ser adicionadas a uma tarefa. Cada [CloudTask] [ py_task] está configurado com uma propriedade de linha de comandos e [ResourceFiles] [ py_resource_file] (como com tarefa de início o agrupamento) que a tarefa de transferências para o nó antes da sua linha de comandos é executada automaticamente. No exemplo, cada tarefa processa apenas um ficheiro. Assim sendo, respectiva colecção de ResourceFiles contém um elemento único.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [AZURE.IMPORTANT] Quando acederem variáveis de ambiente tais como `$AZ_BATCH_NODE_SHARED_DIR` ou executar uma aplicação não encontrada no nó `PATH`, terá de linhas do comando tarefa invocar a shell de explicitamente, tal como com `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. Este requisito se desnecessários se as suas tarefas executem uma aplicação no nó `PATH` e não faça referência quaisquer variáveis de ambiente.

Dentro de `for` loop no fragmento de código acima, pode ver que a linha de comandos para a tarefa é construída com cinco argumentos da linha de comandos que são transmitidos para *python_tutorial_task.py*:

1. **FilePath**: Este é o caminho local para o ficheiro, tal como se encontra no nó. Quando o ResourceFile objecto em `upload_file_to_container` foi criado no passo 2 acima, o nome do ficheiro foi utilizado para esta propriedade (a `file_path` parâmetro no construtor ResourceFile). Isto indica que o ficheiro pode ser encontrado no mesmo directório no nó *python_tutorial_task.py*.

2. **NUMWORDS**: as palavras *N* superiores devem ser escritas para o ficheiro de exportação.

3. **storageaccount**: O nome da conta de armazenamento que é o proprietário o contentor para o qual o resultado da tarefa deve ser carregado.

4. **storagecontainer**: O nome do contentor de armazenamento à qual os ficheiros de resultados devem ser carregados.

5. **sastoken**: A assinatura de acesso partilhado (SA) que fornece acesso de escrita para o contentor de **saída** no armazenamento do Windows Azure. O script *python_tutorial_task.py* utiliza esta assinatura acesso partilhado quando cria a referência de BlockBlobService. Isto fornece acesso de escrita para o contentor sem necessidade de uma tecla de acesso para a conta de armazenamento.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>Passo 6: Monitorizar tarefas

![Monitorizar tarefas][6]<br/>
*O script (1) monitoriza as tarefas para o estado de conclusão e (2) as tarefas carregar dados de resultados para o armazenamento do Windows Azure*

Quando tarefas são adicionadas a uma tarefa, são automaticamente em fila de espera e agendados para execução em nós de cluster dentro do conjunto de associados à tarefa. Com base nas definições que especificar, o lote trata todas as tarefas colocação, agendamento, repetir e outras funções de administração de tarefa para si.

Existem muitos abordagens para monitorizar a execução da tarefa. O `wait_for_tasks_to_complete` função no *python_tutorial_client.py* fornece um exemplo simples de monitorização de tarefas para um determinado Estado, neste caso, a [concluída] [ py_taskstate] estado.

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>Passo 7: Transferir saída de tarefas

![Transferir a saída de tarefas a partir do armazenamento][7]<br/>

Agora que a tarefa está concluída, o resultado das tarefas pode ser transferido a partir do armazenamento do Windows Azure. Esta é executada com uma chamada para `download_blobs_from_container` no *python_tutorial_client.py*:

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [AZURE.NOTE] A chamada para `download_blobs_from_container` *python_tutorial_client.py* Especifica que os ficheiros devem ser transferidos para o seu diretório. Esteja à vontade modificar esta localização de saída.

## <a name="step-8-delete-containers"></a>Passo 8: Contentores de eliminar

Uma vez que são cobrados para os dados que reside armazenamento do Windows Azure, sempre é uma boa ideia para remover quaisquer blobs que já não são necessários para as tarefas de lote. No *python_tutorial_client.py*, esta é executada com três chamadas para [BlockBlobService.delete_container][py_delete_container]:

```
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Passo 9: Eliminar a tarefa e o agrupamento

No passo final, lhe for pedido para eliminar a tarefa e o conjunto de que foram criadas ao script *python_tutorial_client.py* . Apesar de não são cobrados para tarefas e próprias tarefas, *são* cobrado por nós de cluster. Assim sendo, recomendamos que alocar nós apenas conforme necessário. Eliminar conjuntos de dados não utilizados pode ser parte do processo de manutenção.

O BatchServiceClient [JobOperations] [ py_job] e [PoolOperations] [ py_pool] têm ambos os métodos de eliminação correspondentes, chamam-se de que confirme a eliminação:

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [AZURE.IMPORTANT] Tenha em atenção que são cobrados para recursos de cluster – a eliminação de conjuntos de dados não utilizados irão minimizar o custo. Além disso, tenha em atenção de que a eliminação de um conjunto de dados elimina todos os nós de cluster dentro desse conjunto e que quaisquer dados em nós serão irrecuperáveis após o agrupamento é eliminado.

## <a name="run-the-sample-script"></a>Executar o script de exemplo

Quando executa o script *python_tutorial_client.py* do tutorial [código de exemplo][github_article_samples], a saída da consola é semelhante ao seguinte. Existe uma pausa na `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` enquanto nós de cluster no conjunto são criadas, iniciado, e os comandos na tarefa de iniciar o agrupamento são executados. Utilizar o [Azure portal] [ azure_portal] para monitorizar o seu conjunto, calcular nós, tarefa e tarefas durante e após a execução. Utilizar o [Azure portal] [ azure_portal] ou o [Explorador de armazenamento do Microsoft Azure] [ storage_explorer] para ver os recursos de armazenamento (contentores e blobs) que são criados pela aplicação.

>[AZURE.TIP] Executar o script de *python_tutorial_client.py* a partir da `azure-batch-samples/Python/Batch/article_samples` diretório. Utiliza um caminho relativo para a `common.helpers` importar do módulo, por isso que poderá ver `ImportError: No module named 'common'` se não se depare o o script a partir deste diretório.

Tempo de execução típico é **aproximadamente 5-7 minutos** quando executa a amostra na sua configuração predefinida.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>Próximos passos

Esteja à vontade efetuar alterações a *python_tutorial_client.py* e *python_tutorial_task.py* para experimentar com os cenários de cluster diferentes. Por exemplo, experimente adicionar um atraso de execução para *python_tutorial_task.py* para simular tarefas de execução longa e monitorizá-los no portal. Experimente adicionar tarefas mais ou ajustar o número de nós de cluster. Adicione lógica de verificar e permitir a utilização de um conjunto existente para o tempo de execução de velocidade.

Agora que já está familiarizado com o fluxo de trabalho básico de uma solução lote, é hora para aprofundar para as funcionalidades adicionais do serviço do lote.

- Reveja o artigo [Descrição geral do Azure lote funcionalidades](batch-api-basics.md) , que recomendamos que se estiver familiarizado com o serviço.
- Iniciar os outros artigos de desenvolvimento de lote em **desenvolvimento aprofundado** no [caminho de formação do lote][batch_learning_path].
- Dar saída de uma implementação diferente do processamento a carga de trabalho "palavras início N" com lote na [TopNWords] [ github_topnwords] amostra.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage

[pypi_install]: http://python-packaging-user-guide.readthedocs.io/en/latest/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Criar contentores no armazenamento do Windows Azure"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Carregar a aplicação de tarefa e ficheiros de entrada (dados) para contentores"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Criar conjunto de dados Batch"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Criar rotina"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Adicionar tarefas à tarefa"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Monitorizar tarefas"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Transferir a saída de tarefas a partir do armazenamento"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Fluxo de trabalho do lote solução (diagrama completo)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Credenciais do lote no Portal"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Credenciais de armazenamento no Portal"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Fluxo de trabalho do lote solução (diagrama mínimo)"
