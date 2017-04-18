<properties
 pageTitle="Executar ESTRELA-CCM + com HPC Pack no Linux VMs | Microsoft Azure"
 description="Implementar um cluster de pacote de do Microsoft Azure e executar uma ESTRELA-CCM + tarefa em várias Linux calcular nós através de uma rede RDMA."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="xpillons"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/13/2016"
 ms.author="xpillons"/>

# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Executar ESTRELA-CCM + com o Microsoft aos Pack num RDMA Linux cluster no Azure
Este artigo mostra-lhe como implementar um cluster de pacote de do Microsoft Azure e executar uma [ESTRELA CD adapco-CCM +](http://www.cd-adapco.com/products/star-ccm%C2%AE) tarefa em vários nós de cluster Linux que estão interligados com InfiniBand.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft aos Pack disponibiliza funcionalidades para executar uma variedade de HPC em grande escala e aplicações paralelas, incluindo as aplicações do MPI, no clusters de máquinas virtuais do Microsoft Azure. O pacote de aos também suporta as aplicações Linux HPC em execução no Linux cluster nó VMs que são implementadas num cluster aos Pack. Para uma introdução à utilização Linux calcular nós com aos Pack, consulte o [artigo Introdução ao Linux cluster nós num cluster aos Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="set-up-an-hpc-pack-cluster"></a>Configurar um cluster de aos Pack
Transferir os scripts de implementação aos Pack IaaS a partir do [Centro de transferências](https://www.microsoft.com/en-us/download/details.aspx?id=44949) e extrai-los localmente.

Azure PowerShell é um pré-requisito. Se não estiver configurado PowerShell no seu computador local, leia o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

No momento deste escrita, as imagens Linux do Azure Marketplace (que contém os controladores InfiniBand para Azure) são para SLES 12, CentOS 6.5 e CentOS 7.1. Este artigo baseia-se a utilização de SLES 12. Para obter o nome de todas as imagens de Linux que suportam HPC no mercado, pode executar o seguinte comando PowerShell:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

O resultado da lista a localização na qual estas imagens estão disponíveis e o nome da imagem (**nomeimagem**) para ser utilizado no modelo de implementação mais tarde.

Antes de implementar o cluster, tem de criar um ficheiro de modelo de implementação de aos Pack. Uma vez que recomendamos filtragem de um cluster pequenas, o nó cabeça serão o controlador de domínio e alojar uma base de dados SQL local.

O modelo seguinte irá implementar essa um nó cabeça, criar um ficheiro XML denominado **MyCluster.xml**e substitua os valores de **SubscriptionId**, **StorageAccount**, **localização**, **VMName**e **ServiceName** com o seu.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Inicie a criação de cabeça nó ao executar o comando do PowerShell numa linha de comandos elevada:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Depois de 20 a 30 minutos, o nó cabeça deve estar pronto. Pode ligar à mesma a partir do portal Azure ao clicar no ícone de **Ligar** da máquina virtual.

Poderá ter eventualmente corrigir o reencaminhador de DNS. Para fazê-lo, inicie o Gestor de DNS.

1.  Com o botão direito no nome do servidor no Gestor de DNS, clique em **Propriedades**e, em seguida, clique no separador **reencaminhadores** .

2.  Clique no botão **Editar** para remover quaisquer reencaminhadores e, em seguida, clique em **OK**.

3.  Certifique-se de que a caixa de verificação **utilizar sugestões de raiz, se não reencaminhadores disponíveis** está selecionada e, em seguida, clique em **OK**.

## <a name="set-up-linux-compute-nodes"></a>Configurar o Linux cluster nós
Implementar os nós de cluster Linux utilizando o mesmo modelo de implementação que utilizou para criar o nó cabeça.

Copie o ficheiro **MyCluster.xml** a partir do seu computador local para o nó cabeça e atualizar a etiqueta **NodeCount** com o número de nós que pretende implementar (< = 20). Tenha o cuidado de ter o suficiente núcleos disponíveis no seu quota Azure, uma vez que cada instância A9 irá consumir 16 núcleos na sua subscrição. Pode utilizar instâncias de A8 (8 núcleos) em vez de A9 se pretender utilizar mais VMs no mesmo orçamento.

No nó cabeça, copie os scripts de implementação aos Pack IaaS.

Execute os seguintes comandos do Azure PowerShell na linha de comandos elevada:

1.  Execute **Adicionar AzureAccount** para ligar à sua subscrição do Azure.

2.  Se tiver múltiplas subscrições, execute **Get-AzureSubscription** para o listar.

3.  Configurar uma subscrição predefinido ao executar o **Selecione AzureSubscription - SubscriptionName xxxx-predefinido** comando.

4.  Executar **.\New-HPCIaaSCluster.ps1 - ConfigFile MyCluster.xml** para iniciar a implementar o Linux calcular nós.

    ![Implementação de cabeça nó em ação][hndeploy]

Abra a ferramenta de Gestor de clusters HPC Pack. Depois de alguns minutos, Linux cluster nós regularmente irão aparecer na lista de nós do cluster de computação. Com o modo clássico de implementação, IaaS VMs são criados sequencialmente. Por isso, se o número de nós for importante, introdução todos implementado pode demorar um período de tempo significativo.

![Linux nós no Gestor de clusters de pacote HPC][clustermanager]

Agora que todos os nós estão a trabalhar no cluster, existem definições a infraestrutura adicional para fazer.

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>Configurar uma partilha de ficheiros do Azure para Windows e Linux nós
Pode utilizar o serviço de ficheiros do Azure para armazenar scripts, pacotes de aplicações e ficheiros de dados. Ficheiro Azure fornece capacidades CIFS na parte superior de armazenamento de Blobs do Azure como um arquivo persistente. Tenha em atenção que esta não é a solução mais dimensionável, mas é o mais simples e não necessita de VMs dedicadas.

Crie uma partilha de ficheiros do Azure ao seguir as instruções no artigo [Introdução ao armazenamento de ficheiros do Azure no Windows](..\storage\storage-dotnet-how-to-use-files.md).

Manter o nome da sua conta de armazenamento como **saname**, o nome de partilha de ficheiro como **nome de partilha**e a chave de conta de armazenamento como **sakey**.

### <a name="mount-the-azure-file-share-on-the-head-node"></a>Montagem a partilha de ficheiros do Azure no nó cabeça
Abra uma linha de comandos elevada e execute o seguinte comando para armazenar as credenciais no cofre computador local:

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Em seguida, para a partilha de ficheiros do Azure de montagem, execute:

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>Montagem a partilha de ficheiros do Azure em Linux cluster nós
Uma ferramenta útil que vem com aos Pack é a ferramenta de clusrun. Pode utilizar esta ferramenta da linha de comandos para executar o mesmo comando em simultâneo num conjunto de nós de cluster. No nosso caso, é utilizado para montagem a partilha de ficheiros do Azure e persistirem-la para permanecem ser reiniciado.
Na linha de comandos elevada no nó cabeça, execute os seguintes comandos.

Para criar o diretório de montagem:

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Para a partilha de ficheiros do Azure de montagem:

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Para manter a partilha de montagem:

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>Instalar ESTRELA-CCM +
Azure instâncias VM A8 e A9 fornecem suporte InfiniBand e capacidades RDMA. Os controladores de kernel que permitem essas funcionalidades estão disponíveis para Windows Server 2012 R2, SUSE 12, CentOS 6.5 e imagens de CentOS 7.1 no Azure Marketplace. Microsoft MPI e Intel MPI (versão 5. x) são as bibliotecas MPI duas que suportam os controladores no Azure.

ESTRELA de CD adapco-CCM + libertar 11. x e mais tarde integrados com a versão de Intel MPI 5. x, pelo que é incluído InfiniBand suporte para o Azure.

Obter o Linux64 ESTRELA-CCM + pacote a partir do [portal de CD adapco](https://steve.cd-adapco.com). No nosso caso, utilizámos versão 11.02.010 na precisão misto.

No nó cabeça, na partilha de ficheiro do Azure **/hpcdata** , crie um script de shell denominado **setupstarccm.sh** com o seguinte conteúdo. Este script será executada em cada nó cluster para configurar o ESTRELA-CCM + localmente.

#### <a name="sample-setupstarcmsh-script"></a>Exemplo de script setupstarcm.sh
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Agora, para configurar o ESTRELA-CCM + em todos os seus Linux nós de calcular, abra uma linha de comandos elevada e execute o seguinte comando:

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Enquanto o comando estiver em execução, pode monitorizar a utilização da CPU ao utilizar o mapa térmico do Gestor de Cluster. Após alguns minutos, todos os nós devem ser corretamente configurar.

## <a name="run-star-ccm-jobs"></a>Executar ESTRELA-CCM + tarefas
AOS Pack é utilizada para as suas capacidades de Programador de tarefa para poder executar ESTRELA-CCM + tarefas. Para fazê-lo, precisamos de suporte de alguns scripts que são utilizadas para iniciar a tarefa e executar ESTRELA-CCM +. Os dados de entrada são mantidos na partilha de ficheiros do Azure primeiros para simplificar.

O seguinte script do PowerShell é utilizado para fila uma ESTRELA-CCM + tarefa. Bastam três argumentos:

*  O nome de modelo

*  O número de nós para ser utilizado

*  O número de núcleos em cada nó para ser utilizado

Uma vez que ESTRELA-CCM + pode preencher a largura de banda de memória, é normalmente melhor utilizar menos núcleos por nós de cluster e adicionar novos nós. O número exato de núcleos por nó irá dependem da família processador e a velocidade de interligação.

Os nós são atribuídos exclusivamente para a tarefa e não podem ser partilhados com outras tarefas. A tarefa não estiver iniciada como uma tarefa MPI diretamente. O script de shell **runstarccm.sh** será iniciado no iniciador de MPI.

O modelo de entrada e o script **runstarccm.sh** são armazenados na partilhar **/hpcdata** que estava anteriormente instalado.

Ficheiros de registo com o ID da tarefa são denominados e são armazenados na **partilhar /hpcdata**, juntamente com a ESTRELA-CCM + de saída de ficheiros.


#### <a name="sample-submitstarccmjobps1-script"></a>Exemplo de script SubmitStarccmJob.ps1
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job    
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Substitua o seu ESTRELA preferida **runner.java** -iniciador de modelo CCM + Java e o código de registo.

#### <a name="sample-runstarccmsh-script"></a>Exemplo de script runstarccm.sh
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

Nos nossos teste, utilizámos um token de licença do Power a pedido. Para esse token, tem de definir a variável de ambiente de **$CDLMD_LICENSE_FILE** **1999@flex.cd-adapco.com** e a chave na opção **- podkey** da linha de comandos.

Após a algumas inicialização, o script extrai – **$CCP_NODES_CORES** variáveis de ambiente definida por aos Pack – da lista de nós para criar um hostfile que utiliza no iniciador de MPI. Este hostfile irá conter a lista de nomes de nó cluster que são usados para a tarefa, um nome por linha.

O formato do **$CCP_NODES_CORES** segue este padrão:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

Em que:

* `<Number of nodes>`é o número de nós atribuídos a esta tarefa.

* `<Name of node_n_...>`é o nome de cada nó atribuído a esta tarefa.

* `<Cores of node_n_...>`é o número de núcleos no nó atribuídos a esta tarefa.

O número de núcleos (**$NBCORES**) também é calculado com base no número de nós (**$NBNODES**) e o número de núcleos por nó (fornecida como parâmetro **$NBCORESPERNODE**).

Para opções de MPI, são aqueles que são utilizados com Intel MPI no Azure:

*   `-mpi intel`Para especificar Intel MPI.

*   `-fabric UDAPL`Para utilizar o Azure InfiniBand verbos.

*   `-cpubind bandwidth,v`Para otimizar a largura de banda para MPI com ESTRELA-CCM +.

*   `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"`Para tornar MPI Intel trabalhar com Azure InfiniBand e para definir o número necessário de núcleos por nó.

*   `-batch`para iniciar ESTRELA-CCM + no modo de lote com nenhuma IU.


Por fim, para iniciar uma tarefa, certifique-se de que os nós estão a trabalhar e estão online no Gestor de Cluster. Em seguida, a partir de uma linha de comandos do PowerShell, execute este:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>Parar de nós
Posteriormente depois de terminar com o seu testes, pode utilizar os seguintes comandos aos Pack PowerShell para parar e iniciar nós:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>Próximos passos
Tente executar outras cargas de trabalho Linux. Por exemplo, consulte:

* [Execute NAMD com o Microsoft aos Pack em nós de cluster de Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Executar OpenFOAM com o Microsoft aos Pack num cluster Linux RDMA no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)


<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png
