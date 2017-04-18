<properties
 pageTitle="Cluster Linux RDMA para executar as aplicações de MPI | Microsoft Azure"
 description="Criar um cluster de Linux do tamanho H16r, H16mr, A8 ou A9 VMs para utilizar a rede do Azure RDMA para executar MPI aplicações"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Configurar um cluster de Linux RDMA para executar as aplicações de MPI


Saiba como configurar um cluster de Linux RDMA no Azure com [série de H ou com muitos cluster VMs A série](virtual-machines-linux-a8-a9-a10-a11-specs.md) para executar as aplicações de mensagem prisma Interface (MPI) paralelo. Este artigo fornece passos para preparar uma imagem Linux HPC para executar Intel MPI num cluster. Em seguida, implementar um cluster de VMs utilizando esta imagem e uma dos tamanhos RDMA noutros Azure VM (H16r atualmente, H16mr, A8 ou A9). Utilizar o cluster para executar as aplicações de MPI que comunicam eficientemente através de uma latência baixa, rede débito alta com base na remoto acesso directo à memória tecnologia (RDMA).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="cluster-deployment-options"></a>Opções de implementação de cluster

Seguem-se métodos que pode utilizar para criar um cluster de Linux RDMA com ou sem um programador de tarefas.


* **Azure clip scripts** - conforme apresentado mais tarde neste artigo, utilizar a [Interface de comandos do Azure](../xplat-cli-install.md) (CLI) para a implementação de um cluster de VMs RDMA capazes de script. O clip no modo de gestão de serviços cria os nós de cluster em série no modelo clássico de implementação, para que implementar demasiados nós de cluster poderá demorar vários minutos. Para ativar a ligação de rede RDMA quando utiliza o modelo clássico de implementação, implemente VMs no serviço de nuvem do mesmo.

* **Modelos de Gestor de recursos do azure** - também pode utilizar o modelo de implementação do Gestor de recursos para implementar um cluster de VMs RDMA noutros que liga à rede RDMA. Pode [criar o seu próprio modelo](../resource-group-authoring-templates.md), ou verificar o [Guia de introdução Azure modelos](https://azure.microsoft.com/documentation/templates/) para modelos contribuído pela Microsoft ou da Comunidade para implementar a solução que pretende. Os modelos de Gestor de recursos podem fornecem uma forma rápida e fiável para implementar um cluster de Linux. Para ativar a ligação de rede RDMA quando utiliza o modelo de implementação do Gestor de recursos, implemente VMs no mesmo conjunto de disponibilidade.

* **Aos Pack** - crie um cluster de Microsoft aos Pack no Azure e adicione RDMA noutros nós de cluster que executar uma distribuição Linux suportada para aceder à rede RDMA. Consulte o artigo [Introdução ao Linux cluster nós num cluster aos Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-classic-model"></a>Passos de implementação de exemplo no modelo clássico

Os passos seguintes mostram como utilizar o clip do Azure para implementar uma VM de aos SP1 SUSE Linux Enterprise Server (SLES) 12 do Azure Marketplace, personalizá-lo e criar uma imagem VM personalizada. Em seguida, utilize a imagem para a implementação de um cluster de VMs RDMA capazes de script. 

>[AZURE.TIP]Utilize os passos semelhantes para implementar um cluster de VMs RDMA noutros com base em outras imagens HPC suportadas no Azure Marketplace. Alguns passos podem ser diferentes ligeiramente, conforme indicado. Por exemplo, Intel MPI é incluído e configurado em apenas alguns destas imagens. E se implementar uma VM de aos 12 SLES em vez de um SLES 12 SP1 aos VM, terá de atualizar os controladores RDMA. Para obter detalhes, consulte o artigo [sobre como A8, A9, A10 e A11 instâncias de cluster-a com um grau elevado](virtual-machines-linux-a8-a9-a10-a11-specs.md#rdma-driver-updates-for-sles-12).


### <a name="prerequisites"></a>Pré-requisitos

*   **Computador cliente** - precisa de um cliente de baseados no Windows, Mac ou Linux computador para comunicar com o Azure. Estes passos partem do pressuposto de que está a utilizar um cliente Linux.

*   **Subscrição do azure** - se não tiver uma subscrição, pode criar um [livre conta](https://azure.microsoft.com/free/) apenas de duas minutos. Para clusters de maiores dimensões, considere uma subscrição repartição ou outras opções de compra. 

*   **Disponibilidade de tamanho VM** - atualmente os tamanhos de instância seguinte são RDMA capaz: H16r, H16mr, A8 e A9. Consulte os [produtos disponíveis por região](https://azure.microsoft.com/regions/services/) para disponibilidade no Azure regiões. 

*   **Quota núcleos** - é possível que tenha aumentar a quota de núcleos para implementar um cluster de VMs cluster-a com um grau elevado. Por exemplo, precisa de pelo menos 128 núcleos se pretende implementar 8 A9 VMs como é mostrado neste artigo. A sua subscrição também poderá limitar o número de núcleos que pode implementar nas determinadas famílias de tamanho VM, incluindo a série H. Para pedir uma quota aumentar, [Abra um pedido de suporte online do cliente](../azure-supportability/how-to-create-azure-support-request.md) sem encargos. 

*   **Clip Azure** - [instalar](../xplat-cli-install.md) o clip do Azure e [ligar à sua subscrição do Azure](../xplat-cli-connect.md) a partir do computador cliente.


### <a name="step-1-provision-a-sles-12-sp1-hpc-vm"></a>Passo 1. Aprovisionar um VM de HPC 12 SP1 SLES

Depois de iniciar sessão no Azure com o clip do Azure, execute `azure config list` para confirmar que a saída mostra o modo de gestão de serviços. Se não for, defina o modo de executar este comando:


    azure config mode asm


Escreva o seguinte para listar todas as subscrições que estejam autorizados a utilizar:


    azure account list

A subscrição ativa atual é identificada com `Current` definido para `true`. Se esta subscrição não seja aquele que pretende utilizar para criar o cluster, defina o Id da subscrição adequado como a subscrição ativa:

    azure account set <subscription-Id>

Para ver as imagens SLES 12 SP1 aos publicamente disponíveis no Azure, executar um comando semelhante ao seguinte, partindo do princípio que seu de suporta ambiente da shell **grep**:


    azure vm image list | grep "suse.*hpc"

Agora pode Aprovisione um VM RDMA noutros com uma imagem em SLES 12 SP1 ao executar um comando semelhante ao seguinte:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

onde

* O tamanho (A9 neste exemplo) é uma dos tamanhos VM RDMA noutros.

* O número da porta SSH externo (22 neste exemplo, o que é a predefinição SSH) for qualquer número de porta válido. O número de porta SSH interno está definido para 22.

* É criado um novo serviço na nuvem na região Azure especificado pela localização. Especifique uma localização na qual o tamanho da memória virtual que escolher está disponível.

* O nome da imagem SLES 12 SP1 atualmente pode ser `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824` ou `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824` para suporte de prioridade SUSE (aplicadas taxas adicionais).

### <a name="step-2-customize-the-vm"></a>Passo 2. Personalizar a VM

Depois da VM conclui aprovisionamento SSH VM utilizando a VM endereço IP externo (ou nome de DNS) e a porta externa do número que configurado e personalizá-lo. Para detalhes da ligação, consulte o artigo [como iniciar sessão no Linux de executar uma Máquina Virtual](virtual-machines-linux-mac-create-ssh-keys.md). Efetue comandos como o utilizador que configurou no VM, a menos que o acesso de raiz é necessário para concluir um passo.

>[AZURE.IMPORTANT]Microsoft Azure não fornecer acesso de raiz Linux VMs. Para obter acesso administrativo quando ligado como um utilizador para a VM, execute os comandos utilizando `sudo`.

* **Atualizações** - instalar atualizações utilizando **zypper**. Também poderá pretender instalar utilitários NFS. 

    >[AZURE.IMPORTANT]Na VM do SLES 12 SP1 aos, recomendamos que não aplicar atualizações de kernel, que podem causar problemas com a RDMA Linux controladores.

* **Intel MPI** - concluir a instalação do Intel MPI na SLES 12 SP1 aos VM executando o seguinte comando:

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

* **Memória bloquear** - códigos para MPI para bloquear a memória disponível para RDMA, adicionar ou alterar as seguintes definições no ficheiro /etc/security/limits.conf. (Ter acesso de raiz para editar este ficheiro.) 

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

    >[AZURE.NOTE]Para fins de teste, também pode definir memlock como ilimitada. Por exemplo: `<User or group name>    hard    memlock unlimited`. Para mais informações, consulte o artigo [Melhor conhecidos métodos para a definição de tamanho de memória bloqueado](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).

* **Teclas SSH para SLES VMs** - gerar SSH teclas para estabelecer a fidedignidade para a sua conta de utilizador entre os nós de cluster no SLES cluster quando MPI tarefas em execução. (Se implementado uma VM de HPC com base em CentOS, não siga este passo. Consulte as instruções mais tarde no artigo configurar o passwordless SSH fidedignidade entre os nós de cluster depois capturar a imagem em implementar o cluster.) 

    Execute o seguinte comando para criar SSH chaves. Quando lhe for pedido para teclado, prima Enter para gerar as teclas na localização predefinida, sem definir uma frase de acesso.

        ssh-keygen

    Acrescente a chave pública para o ficheiro authorized_keys chaves públicas conhecidos.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    No diretório ~/.ssh, editar ou criar o ficheiro "config". Forneça o intervalo de endereços IP da rede privada que planeia utilizar no Azure (10.32.0.0/16 neste exemplo):

        host 10.32.0.*
        StrictHostKeyChecking no

    Em alternativa, liste o endereço IP de rede privada de cada VM no seu cluster da seguinte forma:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]Configurar `StrictHostKeyChecking no` pode criar um potencial risco de segurança quando um endereço IP específico ou o intervalo não é especificado.

* **Aplicações** - instale quaisquer aplicações que precisa neste VM ou faça outras personalizações antes de capturar a imagem.

### <a name="step-3-capture-the-image"></a>Passo 3. Capturar a imagem

Para capturar a imagem, execute o seguinte comando na Linux VM. Este comando deprovisions a VM, mas mantém as contas de utilizador e as teclas SSH que configurou.

```
sudo waagent -deprovision
```

Em seguida, a partir do computador cliente, execute os seguintes comandos do Azure clip para capturar a imagem. Veja [como capturar uma máquina de virtual Linux clássica como uma imagem](virtual-machines-linux-classic-capture-image.md) para obter detalhes.  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Depois de executar estes comandos, a imagem VM é capturada para a sua utilização e a VM é eliminada. Agora, tem a imagem personalizada pronta para implementar um cluster.

### <a name="step-4-deploy-a-cluster-with-the-image"></a>Passo 4. Implementar um cluster de com uma imagem

Modificar o seguinte script de festa com valores adequados para o seu ambiente e executá-la a partir do seu computador cliente. Uma vez que o Azure implementa VMs em série no modelo de implementação clássico, bastam alguns minutos para implementar o 8 VMs A9 sugeridos neste script.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Considerações para um cluster de CentOS aos

Se pretender configurar um cluster com base das imagens com base em CentOS HPC no mercado Azure em vez de SLES 12 para HPC, siga os passos gerais na secção anterior. Tenha em atenção as diferenças seguintes quando aprovisionar e configurar a VM:

1. Intel MPI já estiver instalado numa VM aprovisionada a partir de uma imagem com base em CentOS HPC. 

2. Definições de bloqueio de memória já são adicionadas no ficheiro de /etc/security/limits.conf a VM.

2. Não gera chaves SSH no VM que aprovisionar para captura. Em vez disso, recomendamos que como configurar uma autenticação baseada em utilizador depois de implementar a cluser. Consulte a secção seguinte.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Configurar o passwordless SSH fidedignidade no cluster

Num cluster HPC com base em CentOS, existem dois métodos para estabelecer confiança entre os nós de cluster: autenticação baseada em anfitrião e autenticação baseada em utilizador. Autenticação baseada em anfitrião está fora do âmbito deste artigo e geralmente deve ser feita através um script extensão durante a implementação. Autenticação baseada em utilizador é conveniente para estabelecer fidedignidade após a implementação e requer a geração e partilha de teclas SSH entre os nós de cluster no cluster. Este método é normalmente conhecido como passwordless SSH login e é necessário quando MPI tarefas em execução. 

Um exemplo de script contribuiu com modelos da Comunidade do está disponível no [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) para activar a autenticação de fácil utilizador num cluster HPC com base em CentOS. Transferir e utilizar este script através dos seguintes passos. Também pode modificar este script ou utilizar qualquer outro método para estabelecer passwordless SSH autenticação entre os nós de cluster de computação.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
Para executar o script, tem de conhecer o prefixo para os seus endereços IP sub-rede. Obter o prefixo executando o seguinte comando sobre um de nós do cluster. O resultado deve ter um aspeto semelhante 10.1.3.5 e o prefixo for o 10.1.3 parte.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Agora executar o script utilizando três parâmetros: o nome de utilizador comuns em nós cluster, a palavra-passe comuns para esse utilizador os nós de cluster e o prefixo de sub-rede que foi devolvido a partir do comando anterior.


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Este script faz o seguinte:

* Cria um diretório chamado .ssh, que é necessário para o início de sessão passwordless o nó do anfitrião. 
* Cria um ficheiro de configuração no diretório .ssh que indica o início de sessão passwordless para permitir o início de sessão a partir de qualquer nó no cluster. 
* Cria ficheiros que contém os nó nomes e endereços IP de nó para todos os nós no cluster. Estes ficheiros são deixados depois de executa o script para referência futura. 
* Cria um par de chaves público e privado para cada nó cluster, incluindo o nó do anfitrião e cria entradas no ficheiro authorized_keys.

>[AZURE.WARNING]Executar este script, pode criar um potencial risco de segurança. Certifique-se de que as informações da chave públicas no ~/.ssh não são distribuídas.


## <a name="configure-intel-mpi"></a>Configurar Intel MPI

Para executar as aplicações de MPI no Azure Linux RDMA, precisa de configurar variáveis determinadas ambiente específicas Intel MPI. Eis um exemplo de script de festa para configurar as variáveis necessárias para executar uma aplicação. Altere o caminho para mpivars.sh conforme necessário para a instalação do Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

O formato do ficheiro anfitrião é da seguinte forma. Adicione uma linha para cada nó no seu cluster. Especifique os endereços IP privados a partir do VNet versões anteriores, não DNS nomes definidos. Por exemplo, para que dois anfitriões com endereços IP 10.32.0.1 e 10.32.0.2 o ficheiro contém o seguinte:

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Executar MPI num cluster de dois nós básico

Se ainda não o fez, configure primeiro o ambiente para Intel MPI. 

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-a-simple-mpi-command"></a>Executar um comando MPI simple

Execute um comando MPI simple dos nós de cluster para mostrar que MPI está instalado corretamente e pode comunicar entre, pelo menos, que dois calcular nós. O seguinte comando **mpirun** executa o comando do **nome do anfitrião** em dois nós.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
O resultado deve listar os nomes de todos os nós passou como entrada de dados para `-hosts`. Por exemplo, um comando **mpirun** com dois nós devolve saída semelhante ao seguinte:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Executar uma referência MPI

O seguinte comando Intel MPI executa uma referência de pingpong para verificar a configuração do cluster e a ligação à rede RDMA.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Num cluster de trabalhar com dois nós, deverá ver o resultado semelhante ao seguinte. Na rede Azure RDMA, espera até 512 bytes tamanhos de latência ou inferior 3 microssegundos para a mensagem.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>Próximos passos

* Experimente implementar e executar a sua MPI Linux aplicações no seu cluster Linux.

* Consulte a [documentação Intel MPI biblioteca](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) para obter orientações sobre Intel MPI.

* Experimente um [modelo de guia de introdução](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) para criar um cluster de Intel cavas utilizando uma imagem com base em CentOS HPC. Para obter detalhes, consulte esta [mensagem de blogue](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/).
