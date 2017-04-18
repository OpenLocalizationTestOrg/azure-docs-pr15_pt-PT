<properties
 pageTitle="Nós de cluster Autoscale aos Pack | Microsoft Azure"
 description="Automaticamente aumentar e diminuir o número de nós de cluster de computação aos Pack no Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Automaticamente aumentar e diminuir os recursos de cluster aos Pack no Azure de acordo com a carga de trabalho de cluster




Se implementar nós Azure "rajada" no seu cluster aos Pack ou criar um cluster de aos Pack no Azure VMs, poderá querer uma forma para aumentar ou diminuir o número de recursos de cluster Azure, tais como nós ou núcleos de acordo com a carga de trabalho atual no cluster automaticamente. Esta opção permite-lhe utilizar forma mais eficiente, os recursos do Azure e controlar os seus custos.
Para executar esta tarefa, configure a propriedade de cluster aos Pack **AutoGrowShrink**. Em alternativa, execute o script do PowerShell aos **AzureAutoGrowShrink.ps1** que é instalado com o pacote de aos.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Além disso, atualmente pode apenas automaticamente aumentar e encolher nós de cluster aos Pack que estiver a executar o sistema operativo Windows Server.

## <a name="set-the-autogrowshrink-cluster-property"></a>Defina a propriedade de cluster AutoGrowShrink

### <a name="prerequisites"></a>Pré-requisitos

* **Aos Pack 2012 R2 Update 2 ou posterior cluster** - pode ser o nó do cabeça cluster implementado quer no local ou numa VM Azure. Consulte o artigo [configurar um cluster de híbrido com aos Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) começar com um nó de cabeça no local e nós do Azure "rajada". Consulte o [script de implementação aos Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para implementar rapidamente um cluster de aos Pack no Azure VMs.


* **Para um cluster com um nó cabeça no Azure** - se utilizar o script de implementação aos Pack IaaS para criar o cluster, ativar a propriedade de cluster **AutoGrowShrink** ao definir a opção AutoGrowShrink no ficheiro de configuração de cluster. Para obter detalhes, consulte a documentação que acompanham o [script transferir](https://www.microsoft.com/download/details.aspx?id=44949). 

    Em alternativa, ative a propriedade de cluster **AutoGrowShrink** depois de implementar o cluster utilizando os comandos do PowerShell aos descritos na secção seguinte. Para preparar para esta situação, primeiro de concluir os passos seguintes:
    1. Configure um certificado de gestão Azure no nó cabeça e na subscrição do Azure. Para uma implementação de teste pode utilizar o certificado autoassinado do Microsoft Azure aos predefinido que instala o pacote de no nó cabeça e simplesmente carregar esse certificado à sua subscrição do Azure. Para opções e os passos, consulte as [orientações da Biblioteca TechNet](https://technet.microsoft.com/library/gg481759.aspx).
    2. Execute a **regedit** no nó cabeça, aceda a HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo e adicionar um novo valor de cadeia. Defina o nome do valor para "Impressão digital" e dados para a impressão digital do certificado no passo 1 do valor.


### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>Comandos do PowerShell de para definir a propriedade AutoGrowShrink

São seguinte de exemplo comandos do PowerShell HPC para definir **AutoGrowShrink** e para ajustar o seu comportamento com parâmetros adicionais. Consulte o artigo [AutoGrowShrink parâmetros](#AutoGrowShrink-parameters) neste artigo para obter a lista completa das definições. 

Para executar estes comandos, comece aos PowerShell no nó de cabeça cluster como administrador.

**Para ativar a propriedade AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 1

**Para desativar a propriedade AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 0

**Para alterar o intervalo de aumentar em minutos**

    Set-HpcClusterProperty –GrowInterval <interval>

**Para alterar o intervalo de encolher em minutos**

    Set-HpcClusterProperty –ShrinkInterval <interval>

**Para ver a configuração atual do AutoGrowShrink**

    Get-HpcClusterProperty –AutoGrowShrink

### <a name="autogrowshrink-parameters"></a>Parâmetros de AutoGrowShrink

Seguem-se AutoGrowShrink parâmetros que pode modificar utilizando o comando **Definir HpcClusterProperty** .

* **EnableGrowShrink** - mudar para ativar ou desativar a propriedade **AutoGrowShrink** .
* **ParamSweepTasksPerCore** - número de tarefas parâmetros varrer para aumentar as uma principais. A predefinição é para aumentar as uma principais por tarefa. 
 
    >[AZURE.NOTE] AOS Pack QFE KB3134307 alterações **ParamSweepTasksPerCore** para **TasksPerResourceUnit**. -Baseia-se o tipo de recurso de trabalho e pode ser nó, socket ou core.
    
* **GrowThreshold** - limiar de tarefas em fila acionado crescimento automático. A predefinição é 1, o que significa que se existirem 1 ou mais tarefas no Estado em fila de espera, automaticamente aumentar nós.
* **GrowInterval** - intervalo em minutos acionado crescimento automático. O intervalo predefinido é de 5 minutos.
* **ShrinkInterval** - intervalo em minutos para acionar a redução automática. O intervalo predefinido é de 5 minutos. |
* **ShrinkIdleTimes** - número de verificações contínuas para encolher para indicar que os nós são inactivos. A predefinição é 3 vezes. Por exemplo, se o **ShrinkInterval** é de 5 minutos, aos Pack verifica se o nó é idle em 5 minutos. Se os nós no estado inactivo depois de 3 contínua verifica (15 minutos), aos Pack diminui nesse nó.
* **ExtraNodesGrowRatio** - adicional percentagem de nós para aumentar as tarefas de mensagem prisma Interface (MPI). O valor predefinido é 1, o que significa que o pacote de aos cresce nós 1% para trabalhos de MPI. 
* **GrowByMin** - mudar para indicar se a política de duplicação é baseada nos recursos mínimos necessários para a tarefa. A predefinição é falsa, o que significa que o pacote de aos cresce nós para tarefas com base no máximos recursos necessários para as tarefas.
* **SoaJobGrowThreshold** - limiar SOA de pedidos de entrada para acionar a automática crescer processo. O valor predefinido é 50000.  
    
    >[AZURE.NOTE] Este parâmetro é suportado a começar aos Pack 2012 R2 atualização 3.
    
* **SoaRequestsPerCore** -número de SOA recebida pedidos para aumentar as uma principais. O valor predefinido é 20000.  

    >[AZURE.NOTE] Este parâmetro é suportado a começar aos Pack 2012 R2 atualização 3.

### <a name="mpi-example"></a>Exemplo MPI

Por predefinição aos Pack cresce 1% nós extra para trabalhos MPI (**ExtraNodesGrowRatio** está definido para 1). O motivo é que MPI precisem de vários nós e a tarefa apenas pode ser executada quando todos os nós estão prontos. Quando o Azure inicia nós, ocasionalmente, um nó poderá ter mais tempo a começar a que as outras a causar outros nós de estar inactivo quando aguardar que o nó preparar-se. Por em crescimento nós extra, aos Pack reduz o tempo de espera este recurso e potencialmente guarda os custos. Para aumentar a percentagem de nós extra para trabalhos MPI (por exemplo, para 10%), executar um comando semelhante ao

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>Exemplo SOA

Por predefinição, **SoaJobGrowThreshold** está definido para 50000 e **SoaRequestsPerCore** está definido para a 200000. Se submeter uma tarefa SOA com pedidos de 70000, será uma tarefa em fila de espera e pedidos recebidos são 70000. Neste caso aos Pack cresce 1 core para a tarefa em fila de espera e para pedidos recebidos, crescimento (70000 50000) / principais 20000 = 1, para que o total de crescimento 2 núcleos para esta tarefa SOA.

## <a name="run-the-azureautogrowshrinkps1-script"></a>Executar o script AzureAutoGrowShrink.ps1

### <a name="prerequisites"></a>Pré-requisitos

* **Cluster posterior ou aos Pack 2012 R2 atualização 1** - o script **AzureAutoGrowShrink.ps1** está instalado na pasta % CCP_HOME % bin. O nó do cabeça cluster pode ser implementada quer no local ou numa VM Azure. Consulte o artigo [configurar um cluster de híbrido com aos Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) começar com um nó de cabeça no local e nós do Azure "rajada". Consulte o artigo o [script de implementação aos Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para implementar rapidamente um cluster de aos Pack no Azure VMs ou utilizar um [modelo de guia de introdução Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).

* **Azure PowerShell 0.8.12** - o script atualmente depende esta versão específica do Azure PowerShell. Se estiver a executar uma versão posterior no nó cabeça, poderá ter que alterar o Azure PowerShell para a [versão 0.8.12](http://az412849.vo.msecnd.net/downloads03/azure-powershell.0.8.12.msi) para executar o script. 

* **Para um cluster connosco de rajada Azure** - executar o script num computador cliente onde está instalado o pacote de aos ou no nó cabeça. Se executar num computador cliente, certifique-se de que o utilizador defina a variável de $env: CCP_SCHEDULER corretamente para apontarem para o nó cabeça. Os nós do Azure "rajada" já tem de ser adicionados ao cluster, mas podem ser no estado de implementado não.


* **Para um cluster de implementada no Azure VMs** - executar o script no nó cabeça VM, uma vez que depende de **HpcIaaSNode.ps1 iniciar** e **Parar HpcIaaSNode.ps1** scripts que são instalados aí. Esses scripts para além disso exigir um certificado de gestão Azure ou publicar o ficheiro de definições (consulte o artigo [Gerir calcular nós num cluster aos Pack no Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)). Certifique-se de que todos o nó cluster VMs precisar já foram adicionadas ao cluster. Podem ser no estado de parado.

### <a name="syntax"></a>Sintaxe

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
### <a name="parameters"></a>Parâmetros

 * **NodeTemplates** - os nomes dos modelos de nó para definir o âmbito para os nós aumentar e diminuir. Se não for especificado (o valor predefinido é @()), todos os nós no grupo nó **AzureNodes** estão no âmbito quando o **tipo de nó** tem um valor de AzureNodes e todos os nós no grupo nó **ComputeNodes** estão no âmbito ao **tipo de nó** tem um valor de ComputeNodes.

 * **JobTemplates** - os nomes dos modelos de projecto para definir o âmbito para os nós de crescimento.

 * **Tipo de nó** - o tipo de nó para aumentar e diminuir. Valores suportados são:

     * **AzureNodes** – para nós Azure PaaS (rajada) no local ou Azure IaaS cluster.

     * **ComputeNodes** - para cluster nó VMs apenas um cluster de Azure IaaS.

* **NumOfQueuedJobsPerNodeToGrow** - número de tarefas em fila necessário para aumentar um nó.

* **NumOfQueuedJobsToGrowThreshold** - o número limite de tarefas em fila para iniciar o processo de aumentar.

* **NumOfActiveQueuedTasksPerNodeToGrow** - o número de tarefas em fila ativas necessário para aumentar um nó. Se não for especificado **NumOfQueuedJobsPerNodeToGrow** com um valor maior que 0, este parâmetro é ignorado.

* **NumOfActiveQueuedTasksToGrowThreshold** - o número limite de tarefas em fila ativas para iniciar o processo de aumentar.

* **NumOfInitialNodesToGrow** - o número mínimo inicial de nós de forma a aumentar, se todos os nós âmbito **Implementado não** ou **parado (Deallocated)**.

* **GrowCheckIntervalMins** - o intervalo em minutos entre os controlos para aumentar as.

* **ShrinkCheckIntervalMins** - o intervalo em minutos entre os controlos para encolher.

* **ShrinkCheckIdleTimes** - o número de contínuo encolher verificações (separadas por **ShrinkCheckIntervalMins**) para indicar os nós são inactivos.

* **UseLastConfigurations** - as configurações anterior guardadas no ficheiro argumento.

* **ArgFile**- o nome do argumento ficheiro utilizado para guardar e atualizar as configurações para executar o script.

* **LogFilePrefix** - o nome do ficheiro de registo de prefixo. Pode especificar um caminho. Por predefinição, o registo é escrito para o directório de trabalho atual.

### <a name="example-1"></a>Exemplo 1

O exemplo seguinte configura os nós de rajada Azure implementados com o modelo de AzureNode predefinido para aumentar e diminuir automaticamente. Se todos os nós inicialmente no estado de **Implementado não** , pelo menos 3 nós são iniciadas. Se o número de tarefas em fila exceder 8, o script inicia nós até a relação entre tarefas em fila para **NumOfQueuedJobsPerNodeToGrow**excede o seu número. Se um nó for encontrado estar inactivo no 3 vezes idle consecutivas, está parado.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>Exemplo 2

O exemplo seguinte configura o nó do Azure cluster VMs implementados com o modelo de ComputeNode predefinido para aumentar e diminuir automaticamente.
As tarefas configuradas pelo modelo de tarefa predefinida definem o âmbito da carga de trabalho no cluster. Se todos os nós inicialmente são parados, pelo menos 5 nós são iniciadas. Se o número de tarefas em fila ativas exceder 15, o script inicia nós até a relação entre tarefas em fila ativas para **NumOfActiveQueuedTasksPerNodeToGrow**excede o seu número. Se um nó for encontrado estar inactivo no 10 vezes idle consecutivas, está parado.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```
