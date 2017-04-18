<properties
 pageTitle="NAMD com Microsoft HPC Pack no Linux VMs | Microsoft Azure"
 description="Implementar um cluster de pacote de do Microsoft Azure e executar uma simulação NAMD com charmrun em vários Linux cluster nós"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="10/13/2016"
 ms.author="danlep"/>

# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Execute NAMD com o Microsoft aos Pack em nós de cluster de Linux no Azure

Este artigo mostra-lhe uma forma para executar uma carga de computação de alto desempenho (HPC) trabalho Linux em máquinas virtuais Azure. Aqui, pode configurar um cluster de [Microsoft aos Pack](https://technet.microsoft.com/library/cc514029) no Azure com Linux cluster nós e executar uma simulação [NAMD](http://www.ks.uiuc.edu/Research/namd/) para calcular e visualizar a estrutura de um sistema de biomolecular grande.  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (para o programa do Nanoscale Molecular Dynamics) é um pacote de paralelas dynamics molecular concebido para simulação de alto desempenho dos sistemas de grandes dimensões biomolecular que contenham até milhões de átomos. Estes sistemas exemplos de vírus, estruturas de célula e proteínas grandes. NAMD escalas para centenas de tarolos para simulações típicas e para mais do que 500.000 tarolos para as simulações maior.

* **Microsoft aos Pack** disponibiliza funcionalidades para executar HPC em grande escala e aplicações paralelas em clusters de computadores no local ou máquinas virtuais Azure. Originalmente desenvolvida como uma solução para o Windows das cargas de trabalho, aos Pack agora suporta alimentado Linux HPC aplicações Linux calcula VMs nó implementados num cluster aos Pack. Consulte o artigo [Introdução ao Linux cluster nós num cluster no Azure aos Pack](virtual-machines-linux-classic-hpcpack-cluster.md) para obter uma introdução.

Para obter outras opções executar Linux HPC das cargas de trabalho no Azure, consulte o artigo [computação de recursos técnicos para o lote e alto desempenho](../batch/batch-hpc-solutions.md).




## <a name="prerequisites"></a>Pré-requisitos

* **Cluster de aos Pack com Linux calcular nós** - implementar um cluster de aos Pack com Linux cluster nós no Azure utilizando um [modelo de Gestor de recursos do Azure](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) ou um [script Azure PowerShell](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Consulte o [artigo Introdução ao Linux cluster nós num cluster aos Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md) para a pré-requisitos e os passos para qualquer uma das opções. Se escolher a opção de implementação de script do PowerShell, consulte o artigo o ficheiro de configuração de exemplo nos ficheiros de exemplo no final deste artigo. Este ficheiro configura um cluster de baseadas no Azure Pack aos que consiste num nó de cabeça do Windows Server 2012 R2 e quatro nós de cluster de grandes dimensões CentOS 6.6 tamanho. Personalize este ficheiro conforme necessário para o seu ambiente.


* **Ficheiros de software e tutorial NAMD** - software de NAMD transferir para Linux a partir do site [NAMD](http://www.ks.uiuc.edu/Research/namd/) (registo necessário). Este artigo é baseado no NAMD versão 2.10 e utiliza o arquivo de [Linux-x86_64 (64 bits Intel/AMD com Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) . Também transferir os [ficheiros da iniciação NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). As transferências são .tar ficheiros e, necessita de uma ferramenta do Windows para extrair os ficheiros no nó de cabeça cluster. Para extrair os ficheiros, siga as instruções neste artigo. 

* **VMD** (opcional) - para ver os resultados da sua tarefa NAMD, transfira e instale o programa de visualização molecular [VMD](http://www.ks.uiuc.edu/Research/vmd/) num computador da sua escolha. A versão atual não for 1.9.2. Consulte o artigo VMD site para começar a transferir.  


## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configurar o fidedignidade comum entre nós de cluster
A execução de uma tarefa de publicação em nó em vários Linux nós requer os nós para confiar umas nas outras (por **rsh** ou **ssh**). Quando cria cluster aos Pack com o script de implementação do Microsoft aos Pack IaaS, o script configura automaticamente permanente fidedignidade comum para a conta de administrador que especificar. Para utilizadores que não sejam administrador que cria no domínio do cluster, tem de configurar o temporária fidedignidade comum entre os nós quando uma tarefa é atribuída aos mesmos. Em seguida, destroy a relação depois da tarefa está concluída. Para fazê-lo para cada utilizador, forneça um par de chaves RSA ao cluster que utiliza aos Pack para estabelecer a relação de fidedignidade. Siga as instruções.

### <a name="generate-an-rsa-key-pair"></a>Gerar um par de chaves RSA
É fácil gerar um par de chaves RSA, que contém uma chave pública e uma chave privada, ao executar o comando de **ssh keygen** Linux.

1.  Inicie sessão no computador Linux.

2.  Execute o seguinte comando:

    ```bash
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Prima **Enter** para utilizar as predefinições até que o comando seja concluído. Não introduza aqui; uma frase de acesso Quando lhe for pedido para uma palavra-passe, basta premir **Enter**.

    ![Gerar um par de chaves RSA][keygen]

3.  Alterar directório ao diretório ~/.ssh. A chave privada é armazenada na id_rsa e da chave pública no id_rsa.pub.

    ![Chaves públicas e privadas][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Adicionar o par de chaves ao cluster aos Pack
1.  [Ligar ao ambiente de trabalho remoto](virtual-machines-windows-connect-logon.md) para o nó cabeça VM utilizando o domínio credenciais desde quando tiver implementado cluster (por exemplo, hpc\clusteradmin). Gerir o cluster a partir do nó cabeça.

2. Utilize procedimentos padrão do Windows Server para criar uma conta de utilizador do domínio no domínio do Active Directory do cluster. Por exemplo, utilize a ferramenta de utilizador do Active Directory e computadores o nó cabeça. Os exemplos neste artigo presumem que cria um utilizador de domínio com o nome hpcuser no domínio de hpclab (hpclab\hpcuser).

3. Adicione o utilizador do domínio ao cluster aos Pack como um utilizador de cluster. Para obter instruções, consulte o artigo [Adicionar ou remover utilizadores de cluster](https://technet.microsoft.com/library/ff919330.aspx).

2.  Criar um ficheiro com o nome C:\cred.xml e copie os dados de chaves RSA na mesma. Pode encontrar um exemplo os ficheiros de exemplo no final deste artigo.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.  Abra uma linha de comandos e introduza o seguinte comando para definir os dados de credenciais da conta hpclab\hpcuser. Utilize o parâmetro **extendeddata** para transmitir o nome do C:\cred.xml ficheiro que criou para os principais dados.

    ```command
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Este comando é concluída com êxito sem saída. Depois de definir as credenciais para contas de utilizador que precisa para executar tarefas, armazenar o ficheiro de cred.xml numa localização segura ou eliminá-la.

5.  Se a par de chaves RSA gerado dos seus nós Linux, lembre-se eliminar as teclas depois de concluir a utilizá-los. AOS Pack não configurar fidedignidade comum se encontra um ficheiro de id_rsa existente ou id_rsa.pub.

>[AZURE.IMPORTANT] Não recomendamos execução de uma tarefa de Linux como um administrador de cluster num cluster partilhado, uma vez que uma tarefa submetida por um administrador executa sob a conta de raiz em nós Linux. Uma tarefa submetida por um utilizador que não sejam administrador é executado numa conta de utilizador Linux local com o mesmo nome que o utilizador de tarefa. Neste caso, aos Pack configura fidedignidade comum para este utilizador Linux em todos os nós atribuídos à tarefa. Pode configurar o utilizador Linux manualmente em nós Linux antes de executar a tarefa ou aos Pack cria o utilizador automaticamente quando a tarefa é submetida. Se o pacote de aos cria o utilizador, aos Pack elimina-lo Depois da tarefa estiver concluída. Para reduzir a ameaça de segurança, as teclas são removidas depois da tarefa estiver concluída em nós.

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurar uma partilha de ficheiros para os nós Linux

Agora configurar uma partilha de ficheiros SMB e montagem na pasta partilhada em todos os nós de Linux para permitir que os nós Linux aceder aos ficheiros NAMD com um caminho comum. Seguem-se os passos para montagem uma pasta partilhada no nó cabeça. Uma partilha é recomendada para as distribuições como CentOS 6.6 atualmente não suportam o serviço de ficheiros do Azure. Se os nós de Linux suportarem uma partilha de ficheiros do Azure, consulte o artigo [como utilizar o armazenamento de ficheiros do Azure com Linux](../storage/storage-how-to-use-files-linux.md). Para opções de partilha com aos Pack de ficheiros adicionais, consulte o artigo [Introdução ao Linux cluster nós num Cluster Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

1.  Criar uma pasta no nó cabeça e partilhá-lo a todos os participantes definindo privilégios de leitura/escrita. Neste exemplo, \\ \\CentOS66HN\Namd é o nome da pasta, onde CentOS66HN é o nome de anfitrião do nó cabeça.

2. Crie uma subpasta denominada namd2 na pasta partilhada. No namd2, crie outra subpasta denominada namdsample.

3. Extrai os ficheiros NAMD na pasta ao utilizar uma versão do Windows do **alcatrão** ou outro utilitário do Windows que funciona no .tar arquivos. 
    * Extrair o arquivo de alcatrão NAMD para \\ \\CentOS66HN\Namd\namd2.
    
    * Extrair os ficheiros de iniciação em \\ \\CentOS66HN\Namd\namd2\namdsample.

4. Abra uma janela do Windows PowerShell e execute os seguintes comandos para a pasta partilhada de montagem em nós Linux.

    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

O primeiro comando cria uma pasta denominada /namd2 em todos os nós no grupo LinuxNodes. O segundo comando montagens //CentOS66HN/Namd/namd2 a pasta partilhada para a pasta com dir_mode e file_mode bits definidos para 777. O *nome de utilizador* e a *palavra-passe* no comando devem ser as credenciais de um utilizador no nó cabeça.

>[AZURE.NOTE]O "\`" símbolo no segundo comando é um símbolo de escape para PowerShell. "\`," significa "," (caráter de vírgula) é uma parte do comando.


## <a name="create-a-bash-script-to-run-a-namd-job"></a>Criar um script de festa para executar uma tarefa NAMD

A tarefa de NAMD necessita de um ficheiro de *nenhuns* para **charmrun** determinar o número de nós para utilizar quando iniciar NAMD processos. Utilize um script de festa que gera o ficheiro nenhuns e executa **charmrun** com este ficheiro nenhuns. Em seguida, pode submeter uma tarefa de NAMD no HPC Cluster Gestor de que chamadas este script.

Utilizando um editor de texto da sua escolha, crie um script de festa na pasta que contém os ficheiros de programa NAMD /namd2 e o nome hpccharmrun.sh. Para uma prova rápida da conceito, copiar o exemplo de script de hpccharmrun.sh fornecido no final deste artigo e aceda ao [Submeter uma tarefa NAMD](#submit-a-namd-job).

>[AZURE.TIP] Guardar o script como um ficheiro de texto com Linux linha terminações (apenas LF, não CR LF). Este procedimento garante que executa corretamente em nós Linux.

Seguem-se detalhes sobre o que significa este script de festa. 

1.  Defina algumas variáveis.

    ```bash
    #!/bin/bash

    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.  Obtenha informações sobre o nó das variáveis de ambiente. $NODESCORES armazena uma lista de palavras dividido a partir do $CCP_NODES_CORES. $COUNT é o tamanho da $NODESCORES.
    ```
    # Get node information from the environment variables
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```    
    
    O formato para a variável CCP_NODES_CORES $ é da seguinte forma:

    ```
    <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
    ```

    Esta variável lista o número total de nós, nomes de nó e número de núcleos em cada nó que estão atribuídos à tarefa. Por exemplo, se a tarefa necessita 10 núcleos para executar, o valor de $CCP_NODES_CORES é semelhante a:

    ```
    3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
    ```
        
3.  Se a variável CCP_NODES_CORES $ não estiver definida, comece **charmrun** diretamente. (Este deverá apenas ocorrem quando executa este script diretamente no seu nós Linux).

    ```
    if [ ${COUNT} -eq 0 ]
    then
        # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
        #echo ${CHARMRUN} $*
        ${CHARMRUN} $*
    ```

4.  Ou crie um ficheiro de nenhuns para **charmrun**.

    ```
    else
        # Create the nodelist file
        NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

        # Write the head line
        echo "group main" > ${NODELIST_PATH}

        # Get every node name and number of cores and write into the nodelist file
        I=1
        while [ ${I} -lt ${COUNT} ]
        do
            echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
            let "I=${I}+2"
        done
```
5.  Execute **charmrun** com o ficheiro de nenhuns, obter o respetivo estado remetente e remover o ficheiro nenhuns no final.

    ${CCP_NUMCPUS} é outra variável de ambiente definido pelo nó cabeça aos Pack. Armazena o número de núcleos total atribuído a esta tarefa. Utilizamos para especificar o número de processos para charmrun.

    ```
    # Run charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
    fi

    ```
6.  Saia com o estado de retorno **charmrun** .

    ```
    exit ${RTNSTS}
    ```



Segue-se as informações no ficheiro de nenhuns, o script gera:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Por exemplo:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Submeter uma tarefa NAMD

Agora, está pronto para submeter uma tarefa NAMD no Gestor de clusters de HPC.

1.  Ligar a sua nó de cabeça cluster e iniciar o Gestor de Cluster HPC.

2.  Em **Gestão de recursos**, certifique-se de que os nós de cluster Linux estão no estado **Online** . Se não estiverem, selecione-as e clique em **Colocar Online**.

2.  Em **Gestão de projectos**, clique em **Nova tarefa**.

3.  Introduza um nome para a tarefa como *hpccharmrun*.

    ![Nova tarefa HPC][namd_job]

4.  Na página **Detalhes de tarefa** , em **Recursos de trabalho**, selecione o tipo de recurso como o **nó** e defina o **mínimo** para 3. , vamos executar a tarefa de três Linux nós e cada nó tem quatro núcleos.

    ![Recursos de projecto][job_resources]

5. Clique em **Editar tarefas** no painel de navegação esquerdo e, em seguida, clique em **Adicionar** para adicionar uma tarefa para a tarefa.    


6. Na página **Detalhes da tarefa e de redirecionamento e/s** , defina os seguintes valores:

    * **Linha de comandos** -
`/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`

        >[AZURE.TIP] Linha de comandos anterior é um único comando sem quebras de linha. Molda-se que seja apresentado em várias linhas em **linha de comandos**.

    * **Directório de trabalho** - /namd2

    * **Mínimo** - 3

    ![Detalhes da tarefa][task_details]

    >[AZURE.NOTE] Defina o directório de trabalho aqui porque **charmrun** tenta navegar para o mesmo directório de trabalho em cada nó. Se o directório de trabalho não estiver definido, o pacote de aos é iniciado o comando numa pasta nomeada aleatoriamente criada num de nós Linux. Isto faz com que o seguinte erro nos outros nós: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` para evitar este problema, especifique um caminho da pasta que pode ser acedido por todos os nós como o directório de trabalho.

5.  Clique em **OK** e, em seguida, clique em **Submeter** para executar esta tarefa.

    Por predefinição, o pacote de aos submete a tarefa como a sua conta de utilizador com sessão iniciada no atual. Uma caixa de diálogo poderá pedir-lhe para introduzir o nome de utilizador e palavra-passe depois de clicar em **Submeter**.

    ![Credenciais do projecto][creds]

    Em algumas condições, aos Pack lembra-se as informações de utilizador antes de entrada e não mostrar esta caixa de diálogo. Para tornar o pacote de aos mostrá-la novamente, introduza o seguinte comando na linha de comandos e, em seguida, submeter a tarefa.

    ```command
    hpccred delcreds
    ```

6.  A tarefa leva vários minutos a concluir.

7.  Localizar o registo de projecto \\ <headnodeName>\Namd\namd2\namd2_hpccharmrun.log e os ficheiros de saída no \\ <headnodeName>\Namd\namd2\namdsample\1-2-sphere\.

8.  Opcionalmente, comece VMD para ver os resultados da sua tarefa. Os passos para visualizar o NAMD de saída de ficheiros (neste caso, uma ubiquitin proteínas molécula um domínio de água) estão para além do âmbito deste artigo. Consulte o artigo [NAMD Tutorial](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) para obter detalhes.

    ![Resultados de tarefa][vmd_view]

## <a name="sample-files"></a>Ficheiros de exemplo

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Ficheiro de configuração de XML de exemplo para a implementação de cluster por PowerShell script

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>    
```

### <a name="sample-credxml-file"></a>Ficheiro de cred.xml de exemplo

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```

### <a name="sample-hpccharmrunsh-script"></a>Exemplo de script hpccharmrun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keys.png
[namd_job]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/namd_job.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/job_resources.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/creds.png
[task_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/task_details.png
[vmd_view]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/vmd_view.png
