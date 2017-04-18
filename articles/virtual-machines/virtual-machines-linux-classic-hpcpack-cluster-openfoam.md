<properties
 pageTitle="Executar OpenFOAM com HPC Pack no Linux VMs | Microsoft Azure"
 description="Implementar um cluster de pacote de do Microsoft Azure e executar uma tarefa de OpenFOAM em vários Linux cluster nós através de uma rede RDMA."
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
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Executar OpenFoam com o Microsoft aos Pack num cluster Linux RDMA no Azure

Este artigo mostra-lhe uma forma de executar OpenFoam em máquinas virtuais do Azure. Aqui, implementar um cluster de Microsoft aos Pack connosco de cluster de Linux no Azure e executar uma [OpenFoam](http://openfoam.com/) cargo com Intel MPI. Pode utilizar noutros RDMA Azure VMs para os nós de cluster, para que os nós de cluster comunicam através da rede do Azure RDMA. Outras opções para executar OpenFoam no Azure incluem completamente configuradas imagens comerciais disponíveis no mercado, tal como [OpenFoam 2.3 no CentOS 6 do UberCloud](https://azure.microsoft.com/marketplace/partners/ubercloud/openfoam-v2dot3-centos-v6/)e, ao executar no [Lote Azure](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM (para abrir operação de campo e manipulação) é um pacote de software de abrir origem utilizaria fluidos dynamics (CFD) que é utilizado em grande escala no engenharia e da ciência, em organizações académicas e comerciais. Inclui ferramentas para meshing, nomeadamente snappyHexMesh, um mesher parallelized para complexas geometrias de CAD e para o pré e pós-processamento. Executar quase todos os processos em paralelo, permitindo aos utilizadores tirar o máximo partido do hardware do computador à sua disposição.  

Microsoft aos Pack disponibiliza funcionalidades para executar HPC em grande escala e aplicações paralelas, incluindo as aplicações do MPI, no clusters de máquinas virtuais do Microsoft Azure. O pacote de aos também suporta a execução Linux HPC aplicações no Linux calcular nó VMs implementados num cluster aos Pack. Consulte o [artigo Introdução ao Linux cluster nós num cluster aos Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md) para uma introdução à utilização Linux cluster nós com aos Pack.

>[AZURE.NOTE] Este artigo ilustra como executar uma carga de trabalho Linux MPI com aos Pack. Parte do princípio de que tem algumas familiaridade com Linux de administração do sistema e em execução das cargas de trabalho MPI em clusters de Linux. Se utilizar versões do MPI e OpenFOAM diferente daqueles neste artigo, poderá ter que modificar alguns passos de instalação e configuração. 

## <a name="prerequisites"></a>Pré-requisitos

*   **Cluster de aos Pack com Linux RDMA noutros calcular nós** - implementar um cluster de aos Pack com tamanho A8, A9, H16r ou H16rm Linux cluster nós utilizando um [modelo de Gestor de recursos do Azure](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) ou um [script Azure PowerShell](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Consulte o [artigo Introdução ao Linux cluster nós num cluster aos Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md) para a pré-requisitos e os passos para qualquer uma das opções. Se escolher a opção de implementação de script do PowerShell, consulte o artigo o ficheiro de configuração de exemplo nos ficheiros de exemplo no final deste artigo. Utilize esta configuração para implementar um cluster de baseadas no Azure Pack aos que consiste num nó de cabeça A8 Windows Server 2012 R2 tamanho e 2 nós de cluster de A8 SUSE Linux Enterprise Server 12 de tamanho. Substituir valores adequados para os nomes de subscrição e serviço. 

    **Adicionais factos a saber**

    *   Para Linux RDMA redes pré-requisitos no Azure, consulte [Acerca da série de H e com muitos cluster VMs A série](virtual-machines-windows-a8-a9-a10-a11-specs.md).

    *   Se utilizar a opção de implementação de script do Powershell, implemente todos os nós de cluster Linux dentro de serviço em um nuvem para utilizar a ligação de rede RDMA.

    *   Depois de o implementar os nós Linux, ligar-se ao SSH para efetuar as tarefas administrativas adicionais. Localize os detalhes de ligação SSH para cada VM Linux no portal do Azure.  
        
*   **Intel MPI** - para executar o OpenFOAM SLES 12 aos calcular nós no Azure, tem de instalar o runtime Intel MPI biblioteca 5 a partir do [Intel.com site](https://software.intel.com/en-us/intel-mpi-library/). (Intel MPI 5 está pré-instalado em imagens com base em CentOS HPC.)  Um passo mais tarde, se necessário, instale Intel MPI no seu nós de cluster Linux. Para preparar para neste passo, depois de registar com Intel, siga a ligação na mensagem de confirmação para a página web relacionadas. Em seguida, copie a ligação de transferência para o ficheiro. tgz para a versão adequada do Intel MPI. Este artigo é baseado em Intel MPI versão 5.0.3.048.

*   **OpenFOAM origem Pack** - transferir o software OpenFOAM origem Pack para Linux a partir do [site OpenFOAM Foundation](http://openfoam.org/download/2-3-1-source/). Este artigo é baseado na versão de origem Pack 2.3.1, disponível para transferência como OpenFOAM 2.3.1.tgz. Siga as instruções neste artigo para descompactar e compilar OpenFOAM em nós de cluster Linux.

*   **EnSight** (opcional) - para ver os resultados da sua simulação OpenFOAM, transfira e instale o programa de visualização e de análise de [EnSight](https://www.ceisoftware.com/download/) . Informações de licenciamento e transferência estiver no EnSight site.


## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configurar o fidedignidade comum entre nós de cluster

A execução de uma tarefa de publicação em nó em vários Linux nós requer os nós para confiar umas nas outras (por **rsh** ou **ssh**). Quando cria cluster aos Pack com o script de implementação do Microsoft aos Pack IaaS, o script configura automaticamente permanente fidedignidade comum para a conta de administrador que especificar. Para utilizadores que não sejam administrador que cria no domínio do cluster, tem de configurar temporária fidedignidade comum entre os nós quando uma tarefa é atribuída aos mesmos e destroy a relação depois da tarefa está concluída. Para estabelecer fidedignidade para cada utilizador, forneça um par de chaves RSA ao cluster de que o pacote de aos utiliza para a relação de fidedignidade.

### <a name="generate-an-rsa-key-pair"></a>Gerar um par de chaves RSA

É fácil gerar um par de chaves RSA, que contém uma chave pública e uma chave privada, ao executar o comando de **ssh keygen** Linux.

1.  Inicie sessão no computador Linux.

2.  Execute o seguinte comando:

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Prima **Enter** para utilizar as predefinições até que o comando seja concluído. Não introduza aqui; uma frase de acesso Quando lhe for pedido para uma palavra-passe, basta premir **Enter**.

    ![Gerar um par de chaves RSA][keygen]

3.  Alterar directório ao diretório ~/.ssh. A chave privada é armazenada na id_rsa e da chave pública no id_rsa.pub.

    ![Chaves públicas e privadas][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Adicionar o par de chaves ao cluster aos Pack
1.  Efetue uma ligação de ambiente de trabalho remoto para o nó do seu cabeça com a sua conta de administrador aos Pack (a conta de administrador que configurar quando executou o script de implementação).

2. Utilize procedimentos padrão do Windows Server para criar uma conta de utilizador do domínio no domínio do Active Directory do cluster. Por exemplo, utilize a ferramenta de utilizador do Active Directory e computadores o nó cabeça. Os exemplos neste artigo presumem que cria um utilizador de domínio com o nome hpclab\hpcuser.

3.  Criar um ficheiro com o nome C:\cred.xml e copie os dados de chaves RSA na mesma. É um ficheiro de cred.xml de exemplo no final deste artigo.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

4.  Abra uma linha de comandos e introduza o seguinte comando para definir os dados de credenciais da conta hpclab\hpcuser. Utilize o parâmetro **extendeddata** para passar o nome do ficheiro C:\cred.xml que criou para os principais dados.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Este comando é concluída com êxito sem saída. Depois de definir as credenciais para contas de utilizador que precisa para executar tarefas, armazenar o ficheiro de cred.xml numa localização segura ou eliminá-la.

5.  Se a par de chaves RSA gerado dos seus nós Linux, lembre-se eliminar as teclas depois de concluir a utilizá-los. Se o pacote de aos localizar um ficheiro de id_rsa existente ou id_rsa.pub,-não configurar fidedignidade comum.

>[AZURE.IMPORTANT] Não recomendamos execução de uma tarefa de Linux como um administrador de cluster num cluster partilhado, uma vez que uma tarefa submetida por um administrador executa sob a conta de raiz em nós Linux. No entanto, uma tarefa submetida por um utilizador que não sejam administrador é executado numa conta de utilizador Linux local com o mesmo nome que o utilizador de tarefa. Neste caso, aos Pack configura fidedignidade comum para este utilizador Linux em todos os nós atribuídos à tarefa. Pode configurar o utilizador Linux manualmente em nós Linux antes de executar a tarefa ou aos Pack cria o utilizador automaticamente quando a tarefa é submetida. Se o pacote de aos cria o utilizador, aos Pack elimina-lo Depois da tarefa estiver concluída. Para reduzir ameaças de segurança, o pacote de aos remove as teclas após a conclusão da tarefa.

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurar uma partilha de ficheiros para os nós Linux

Configure uma partilha SMB padrão numa pasta no nó cabeça agora. Para permitir que os nós Linux aceder aos ficheiros de aplicação com um caminho comum, montagem na pasta partilhada em nós Linux. Se pretender, pode utilizar outra opção, tal como uma partilha de ficheiros do Azure - recomendada para muitos cenários - ou numa partilha NFS de partilha de ficheiros. Consulte os informações e passos detalhados no [artigo Introdução ao Linux cluster nós num Cluster Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md)de partilha de ficheiros.

1.  Criar uma pasta no nó cabeça e partilhá-lo a todos os participantes definindo privilégios de leitura/escrita. Por exemplo, partilhar C:\OpenFOAM no nó cabeça como \\ \\SUSE12RDMA HN\OpenFOAM. Aqui, *SUSE12RDMA HN* é o nome do anfitrião do nó cabeça.

2.  Abra uma janela do Windows PowerShell e execute os seguintes comandos:

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

    clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

O primeiro comando cria uma pasta denominada /openfoam em todos os nós no grupo LinuxNodes. O segundo comando montagens //SUSE12RDMA-HN/OpenFOAM a pasta partilhada em nós Linux com dir_mode e file_mode bits definidos para 777. O *nome de utilizador* e a *palavra-passe* no comando devem ser as credenciais de um utilizador no nó cabeça.

>[AZURE.NOTE]O "\`" símbolo no segundo comando é um símbolo de escape para PowerShell. "\`," significa "," (caráter de vírgula) é uma parte do comando.

## <a name="install-mpi-and-openfoam"></a>Instalar o MPI e OpenFOAM

Para executar OpenFOAM como uma tarefa MPI na rede RDMA, é necessário compilar OpenFOAM com as bibliotecas Intel MPI. 

Em primeiro lugar, execute vários comandos **clusrun** para instalar bibliotecas Intel MPI (se ainda não tiver instalado) e OpenFOAM no seu nós Linux. Utilize a partilha de cabeça nó configurada anteriormente para partilhar os ficheiros de instalação entre os nós Linux.

>[AZURE.IMPORTANT]Estes instalação e compilar passos são exemplos. Precisa de alguns dados de conhecimento da Linux administração do sistema para se certificar de que elaboração dependentes e bibliotecas estão instaladas corretamente. Poderá ter de modificar variáveis determinadas ambiente ou outras definições para as versões do Intel MPI e OpenFOAM. Para obter detalhes, consulte o artigo [Intel MPI biblioteca para o guia de instalação Linux](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) e [OpenFOAM origem pacote de instalação](http://openfoam.org/download/2-3-1-source/) para o seu ambiente.


### <a name="install-intel-mpi"></a>Instalar o Intel MPI

Guarde o pacote de instalação transferido para MPI Intel (l_mpi_p_5.0.3.048.tgz neste exemplo) no C:\OpenFoam no nó cabeça para que os nós Linux podem aceder a este ficheiro a partir do /openfoam. Em seguida, execute **clusrun** para instalar a biblioteca de Intel MPI em todos os nós de Linux.

1.  Os seguintes comandos copiar o pacote de instalação e extraia-o para /opt/intel em cada nó.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel

    clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
    ```

2.  Para instalar silenciosamente Intel MPI biblioteca, utilize um ficheiro de silent.cfg. Pode encontrar um exemplo os ficheiros de exemplo no final deste artigo. Coloque este ficheiro na /openfoam a pasta partilhada. Para obter detalhes sobre o ficheiro silent.cfg, consulte o artigo [Intel MPI biblioteca para o guia de instalação do Linux - instalação silenciosa](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).

    >[AZURE.TIP]Certifique-se de que linha que guardar o seu ficheiro silent.cfg como um ficheiro de texto com o Linux terminações (apenas LF, não CR LF). Este passo garante que executa corretamente em nós Linux.

3.  Instale Intel MPI biblioteca no modo silencioso.
 
    ```
    clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
    ```
    
### <a name="configure-mpi"></a>Configurar MPI

Para testar a ligação, deve adicionar as linhas seguintes para /etc/security/limits.conf em cada um de nós Linux:


    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Reinicie os nós de Linux depois de atualizar o ficheiro limits.conf. Por exemplo, utilize o seguinte comando **clusrun** :

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Após reiniciar, certifique-se de que a pasta partilhada é instalada como /openfoam.

### <a name="compile-and-install-openfoam"></a>Compilar e instalar o OpenFOAM

Guarde o pacote de instalação transferido para o pacote de origem OpenFOAM (2.3.1.tgz OpenFOAM neste exemplo) para C:\OpenFoam no nó cabeça para que os nós Linux podem aceder a este ficheiro a partir do /openfoam. Em seguida, execute **clusrun** comandos para compilar OpenFOAM em todos os nós de Linux.


1.  Criar uma pasta /opt/OpenFOAM em cada nó Linux, copie o pacote de origem para esta pasta e extraia-aí.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM

    clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
    
    clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
    ```

2.  Para compilar OpenFOAM com a biblioteca de MPI Intel, configure primeiro algumas variáveis de ambiente para Intel MPI e OpenFOAM. Utilize um script de festa denominado settings.sh para definir as variáveis. Pode encontrar um exemplo os ficheiros de exemplo no final deste artigo. Coloque este ficheiro (guardado com terminações das linhas Linux) na /openfoam a pasta partilhada. Este ficheiro também contém as definições para a runtimes MPI e OpenFOAM que utilizar mais tarde para executar uma tarefa OpenFOAM.

3. Instale pacotes dependentes necessários para compilar OpenFOAM. Dependendo da sua distribuição Linux, pela primeira vez poderá ter de adicionar um repositório. Execute **clusrun** comandos semelhantes ao seguinte:

    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
    
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
    
    Se for necessário, SSH para cada nó Linux para executar os comandos para confirmar que funcionam corretamente.

4.  Execute o seguinte comando para compilar OpenFOAM. O processo de compilação demora algum tempo a concluir e gera uma grande quantidade de informações de registo de saída padrão, pelo que a opção **/ intercalado** para apresentar o resultado intercalado.

    ```
    clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
    ```
    
    >[AZURE.NOTE]O "\`" símbolo no comando é um símbolo de escape para PowerShell. "\`&" significa que a "&" é uma parte do comando.

## <a name="prepare-to-run-an-openfoam-job"></a>Preparar para executar uma tarefa OpenFOAM

Prepare-se agora executar uma tarefa MPI designado por sloshingTank3D, que é uma das amostras OpenFoam, dois nós Linux. 

### <a name="set-up-the-runtime-environment"></a>Configurar o ambiente do runtime

Para configurar os ambientes de runtime MPI e OpenFOAM em nós Linux, execute o seguinte comando numa janela do Windows PowerShell no nó cabeça. (Este comando é válido para SUSE Linux apenas.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Preparar os dados de exemplo

Utilize a partilha de cabeça nó que configurou anteriormente para partilhar ficheiros entre os nós de Linux (instalados como /openfoam).

1.  SSH para um dos seus Linux calcular nós.

2.  Execute o seguinte comando para configurar o ambiente do runtime OpenFOAM, se ainda não o tiver já feito.

    ```
    $ source /openfoam/settings.sh
    ```
    
3.  Copiar o exemplo sloshingTank3D para a pasta partilhada e navegue para a mesma.

    ```
    $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/

    $ cd /openfoam/sloshingTank3D
    ```

4.  Quando utiliza os parâmetros de predefinido de neste exemplo, pode demorar dezenas de minutos para executar, para que poderá pretender modificar alguns parâmetros para que seja mais rápido. Uma escolha simple é modificar o tempo passo variáveis deltaT e writeInterval no ficheiro do sistema/controlDict. Este ficheiro armazena todos os dados de entrada relacionados com o controlo de tempo e de leitura e escrita solução dados. Por exemplo, pode alterar o valor da deltaT a partir do 0.05 0,5 e o valor de writeInterval a partir do 0.05 0,5.

    ![Modificar passo variáveis][step_variables]

5.  Especificar valores pretendidos para as variáveis no ficheiro de sistema/decomposeParDict. Este exemplo utiliza duas nós Linux cada com 8 núcleos, por isso, definido numberOfSubdomains a 16 e n de hierarchicalCoeffs para (1 1 16), que significa que executa OpenFOAM em paralelo com os processos de 16. Para obter mais informações, consulte o artigo [OpenFOAM manual do utilizador: 3.4 aplicações em execução em paralelo](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).

    ![Decomposição processos][decompose]

6.  Execute os seguintes comandos do diretório sloshingTank3D para preparar os dados de exemplo.

    ```
    $ . $WM_PROJECT_DIR/bin/tools/RunFunctions

    $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict

    $ runApplication blockMesh

    $ cp 0/alpha.water.org 0/alpha.water

    $ runApplication setFields  
    ```
    
7.  No nó cabeça, deverá ver que os ficheiros de dados de exemplo são copiados para C:\OpenFoam\sloshingTank3D. (C:\OpenFoam é a pasta partilhada no nó cabeça.)

    ![Ficheiros de dados no nó cabeça][data_files]

### <a name="host-file-for-mpirun"></a>Ficheiro de anfitrião para mpirun

Neste passo, cria um ficheiro de anfitrião (uma lista de nós do cluster) que utiliza o comando **mpirun** .

1.  Dos nós Linux, crie um ficheiro denominado hostfile em /openfoam, para que este ficheiro esteja contactável /openfoam/hostfile em todos os nós Linux.

2.  Escreva os nomes de nó Linux para este ficheiro. Neste exemplo, o ficheiro contém os nomes dos seguintes procedimentos:
    
    ```       
    SUSE12RDMA-LN1
    SUSE12RDMA-LN2
    ```
    
    >[AZURE.TIP]Também pode criar este ficheiro na C:\OpenFoam\hostfile no nó cabeça. Se selecionar esta opção, guardá-lo como um ficheiro de texto com o Linux linha terminações (apenas LF, não CR LF). Este procedimento garante que executa corretamente em nós Linux.

    **Festa espacial script**

    Se tiver muitos Linux nós e pretende que o seu trabalho a ser executado em apenas alguns dos mesmos, não é uma boa ideia para utilizar um ficheiro de anfitrião fixo, porque não sabe quais os nós serão atribuídos para o seu trabalho. Neste caso, escreva um dispositivo de moldagem de script de festa para **mpirun** criar o ficheiro de anfitrião automaticamente. Pode encontrar um bombom de script de festa de exemplo denominado hpcimpirun.sh no final deste artigo e guardá-lo como /openfoam/hpcimpirun.sh. Este exemplo de script faz o seguinte:

    1.  Configura as variáveis de ambiente para **mpirun**e alguns parâmetros de comando de adição para executar a tarefa MPI através da rede RDMA. Neste caso, define as variáveis que se seguem:

        *   I_MPI_FABRICS = shm:dapl
        *   I_MPI_DAPL_PROVIDER = ofa-v2-ib0
        *   I_MPI_DYNAMIC_CONNECTION = 0

    2.  Cria um ficheiro de anfitrião de acordo com o ambiente $ variável CCP_NODES_CORES, que está definido pelo nó cabeça HPC quando a tarefa é activada.

        O formato do $CCP_NODES_CORES segue este padrão:

        ```
        <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
        ```

        onde

        * `<Number of nodes>`-o número de nós atribuídos a esta tarefa.  
        
        * `<Name of node_n_...>`-o nome de cada nó atribuído a esta tarefa.
        
        * `<Cores of node_n_...>`-o número de núcleos no nó atribuídos a esta tarefa.

        Por exemplo, se a tarefa necessita de dois nós para executar, $CCP_NODES_CORES é semelhante a
        
        ```
        2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
        ```
        
    3.  Liga para o comando **mpirun** e acrescenta dois parâmetros à linha de comandos.

        * `--hostfile <hostfilepath>: <hostfilepath>`-o caminho do ficheiro anfitrião cria o script

        * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}`-uma variável de ambiente definida pelo nó cabeça aos Pack, que armazena o número de núcleos total atribuído a esta tarefa. Neste caso, especifica o número de processos para **mpirun**.


## <a name="submit-an-openfoam-job"></a>Submeter uma tarefa OpenFOAM

Agora pode submeter uma tarefa no HPC Cluster Manager. Tem de ser efetuada com o hpcimpirun.sh script nas linhas de comando para algumas das tarefas de projecto.

1. Ligar a sua nó de cabeça cluster e iniciar o Gestor de Cluster HPC.

2. **Em gestão de recursos**, certifique-se de que os nós de cluster Linux estão no estado **Online** . Se não estiverem, selecione-as e clique em **Colocar Online**.

3.  Em **Gestão de projectos**, clique em **Nova tarefa**.

4.  Introduza um nome para a tarefa como _sloshingTank3D_.

    ![Detalhes de tarefa][job_details]

5.  **Recursos de trabalho**, selecione o tipo de recurso como "Nó" e configure o mínimo para 2. Esta configuração executa a tarefa em dois nós Linux, cada um dos quais tem oito núcleos neste exemplo.

    ![Recursos de projecto][job_resources]

6. Clique em **Editar tarefas** no painel de navegação esquerdo e, em seguida, clique em **Adicionar** para adicionar uma tarefa para a tarefa. Adicione quatro tarefas para a tarefa com as seguintes definições e linhas de comandos.

    >[AZURE.NOTE]Executar `source /openfoam/settings.sh` configura os ambientes de runtime OpenFOAM e MPI, para cada uma das seguintes tarefas a chamadas antes do comando OpenFOAM.

    *   **Tarefa 1**. Execute **decomposePar** para gerar ficheiros de dados para a execução **interDyMFoam** em paralelo.
    
        *   Atribuir um nó à tarefa

        *   **Linha de comandos** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
    
        *   **Directório de trabalho** - / openfoam/sloshingTank3D
        
        Consulte o artigo na figura seguinte. Configurar as tarefas restantes de forma semelhante.

        ![Detalhes da tarefa 1][task_details1]

    *   **Tarefa 2**. Execute **interDyMFoam** em paralelo para calcular a amostra.

        *   Atribuir dois nós à tarefa

        *   **Linha de comandos** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`

        *   **Directório de trabalho** - / openfoam/sloshingTank3D

    *   **Tarefa 3**. Execute **reconstructPar** intercalar os conjuntos de directórios de hora a partir do diretório cada processor_N_ um único conjunto.

        *   Atribuir um nó à tarefa

        *   **Linha de comandos** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`

        *   **Directório de trabalho** - / openfoam/sloshingTank3D

    *   **Tarefa 4**. Execute **foamToEnsight** em paralelo para converter os ficheiros de resultado OpenFOAM num formato de EnSight e colocar os ficheiros de EnSight num diretório chamado Ensight no diretório maiúsculas e minúsculas.

        *   Atribuir dois nós à tarefa

        *   **Linha de comandos** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`

        *   **Directório de trabalho** - / openfoam/sloshingTank3D

6.  Adicione dependências a estas tarefas por tarefa ordem ascendente.

    ![Dependências de tarefas][task_dependencies]

7.  Clique em **Submeter** para executar esta tarefa.

    Por predefinição, o pacote de aos submete a tarefa como a sua conta de utilizador com sessão iniciada no atual. Depois de clicar em **Submeter**, poderá ver uma caixa de diálogo lhe pedir para introduzir o nome de utilizador e palavra-passe.

    ![Credenciais do projecto][creds]

    Em algumas condições, aos Pack lembra-se as informações de utilizador antes de entrada e não mostrar esta caixa de diálogo. Para tornar o pacote de aos mostrá-la novamente, introduza o seguinte comando na linha de comandos e, em seguida, submeter a tarefa.

    ```
    hpccred delcreds
    ```

8.  A tarefa leva a partir de dezenas de minutos para várias horas de acordo com os parâmetros que definiu para a amostra. No mapa térmico, verá o trabalho em execução nos nós Linux. 

    ![Mapa térmico][heat_map]

    Em cada nó, são iniciados os processos de oito.

    ![Linux processos][linux_processes]

9.  Quando termina a tarefa, localize os resultados da tarefa em pastas em C:\OpenFoam\sloshingTank3D e os ficheiros de registo na C:\OpenFoam.


## <a name="view-results-in-ensight"></a>Ver os resultados em EnSight

Opcionalmente, utilize [EnSight](https://www.ceisoftware.com/) para visualizar e analisar os resultados da tarefa OpenFOAM. Para mais informações sobre a visualização e animações no EnSight, consulte o artigo neste [Guia de vídeo](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1.  Depois de instalar EnSight no nó cabeça, iniciá-lo.

2.  Abra C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.

    Consulte no Visualizador de combustível.

    ![Combustível no EnSight][tank]

3.  Criar um **Isosurface** a partir de **internalMesh**e, em seguida, selecione a variável **alpha_water**.

    ![Criar um isosurface][isosurface]

4.  Defina a cor de **Isosurface_part** criado no passo anterior. Por exemplo, configurá-lo a água azul.

    ![Editar isosurface cor][isosurface_color]

5.  Criar um **Iso volume** a partir do **stencil paredes** selecionando **paredes** no painel de **peças** e clique no botão **Isosurfaces** na barra de ferramentas.

6.  Na caixa de diálogo, selecione **tipo** como **Isovolume** e defina o mínimo de **intervalo Isovolume** 0,5. Para criar o isovolume, clique em **criar com determinadas partes**.

7.  Defina a cor de **Iso_volume_part** criado no passo anterior. Por exemplo, configurá-lo a água abrangente azul.

8.  Defina a cor para que **as paredes**. Por exemplo, configurá-lo a branco transparente.

9. Agora clique em **Reproduzir** para ver os resultados de simulação.

    ![Resultado de combustível][tank_result]

## <a name="sample-files"></a>Ficheiros de exemplo

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Ficheiro de configuração de XML de exemplo para a implementação de cluster por PowerShell script

 ```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>suse12rdmavnet</VNetName>
    <SubnetName>SUSE12RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>SUSE12RDMA-HN</VMName>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <NodeCount>2</NodeCount>
      <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
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
### <a name="sample-silentcfg-file-to-install-mpi"></a>Ficheiro de silent.cfg de exemplo para instalar MPI

```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no


```

### <a name="sample-settingssh-script"></a>Exemplo de script settings.sh

```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


###<a name="sample-hpcimpirunsh-script"></a>Exemplo de script hpcimpirun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
    ${MPIRUN} $*
else
    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

    # Get every node name and write into the hostfile file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the mpirun with hostfile arg
    ${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keys.png
[step_variables]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/step_variables.png
[data_files]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/data_files.png
[decompose]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/decompose.png
[job_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_details.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_resources.png
[task_details1]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_dependencies.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/creds.png
[heat_map]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/heat_map.png
[tank]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank.png
[tank_result]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank_result.png
[isosurface]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/linux_processes.png
