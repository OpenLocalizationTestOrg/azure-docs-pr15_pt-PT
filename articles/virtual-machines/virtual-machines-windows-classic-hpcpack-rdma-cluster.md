<properties
 pageTitle="Configurar um cluster de RDMA do Windows para executar as aplicações de MPI | Microsoft Azure"
 description="Saiba como criar um cluster de Windows aos Pack com tamanho de H16r, H16mr, A8 ou A9 VMs para utilizar a rede do Azure RDMA para executar MPI aplicações."
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
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/20/2016"
 ms.author="danlep"/>

# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Configurar um cluster de Windows RDMA com aos Pack para executar as aplicações de MPI

Configure um cluster de RDMA do Windows Azure com [Microsoft aos Pack](https://technet.microsoft.com/library/cc514029) e [série H ou com muitos cluster instâncias de uma série](virtual-machines-windows-a8-a9-a10-a11-specs.md) para executar as aplicações de mensagem prisma Interface (MPI) paralelo. Quando configurar RDMA noutros, com base no Windows Server nós num cluster aos Pack, aplicações MPI comunicam eficientemente através de uma latência baixa, rede de débito alta no Azure que é baseado no tecnologia de acesso (RDMA) de memória direta remoto.

Se pretender executar das cargas de trabalho MPI no VMs Linux que aceder à rede do Azure RDMA, consulte o artigo [configurar um cluster de Linux RDMA para executar MPI aplicações](virtual-machines-linux-classic-rdma-cluster.md).


## <a name="hpc-pack-cluster-deployment-options"></a>Opções de implementação de cluster aos Pack
Microsoft aos Pack é uma ferramenta desde sem custos adicionais para criar HPC clusters no local ou no Azure para executar o Windows ou Linux HPC aplicações. AOS pacote inclui um ambiente do runtime para a implementação do mensagem prisma Interface para Windows (MS MPI) da Microsoft. Quando utilizado com instâncias RDMA noutros a executar o sistema operativo Windows Server suportado, aos Pack fornece uma opção para executar as aplicações do Windows MPI que acedam à rede do Azure RDMA eficiente. 

Este artigo apresenta os dois cenários e ligações para orientações detalhadas para configurar um cluster de RDMA do Windows com o Microsoft aos Pack. 

* Cenário 1. Implementar instâncias de função de trabalho com muitos cluster (PaaS)

* Cenário 2. Implementar nós cluster com muitos cluster VMs (IaaS)

Para pré-requisitos gerais utilizar com muitos cluster instâncias com o Windows, consulte [Acerca da série de H e com muitos cluster VMs A série](virtual-machines-windows-a8-a9-a10-a11-specs.md) .



## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Cenário 1. Implementar instâncias de função de trabalho com muitos cluster (PaaS)

A partir de um cluster de aos Pack existente, adicione recursos do cluster extra em instâncias de função trabalhador Azure (nós Azure) em execução num serviço de nuvem (PaaS). Esta funcionalidade, também denominada "rajada para Azure" a partir do pacote de aos, suporta um intervalo de tamanhos para as instâncias de função de trabalho. Ao adicionar os nós Azure, especifique simplesmente a um dos tamanhos RDMA noutros.

Seguem-se considerações e passos para rajada para RDMA noutros instâncias Azure a partir de uma existente (normalmente no local) cluster. Utilize semelhantes procedimentos para adicionar instâncias de função de trabalho para um nó de cabeça aos Pack que é implementado numa VM Azure.

>[AZURE.NOTE] Para obter um tutorial para rajada para Azure com aos Pack, consulte o artigo [configurar um cluster de híbrido com aos Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Tenha em atenção as considerações nos passos abaixo aplicáveis especificamente RDMA noutros nós Azure.

![Rajada para Azure][burst]

### <a name="steps"></a>Passos

4. **Implementar e configurar um nó de cabeça aos Pack 2012 R2**

    Transferir o pacote de instalação aos Pack mais recente a partir do [Centro de transferências da Microsoft](https://www.microsoft.com/download/details.aspx?id=49922). Para requisitos e instruções para se preparar para uma implementação do Azure rajada, consulte o artigo [Aos Pack guia de introdução](https://technet.microsoft.com/library/jj884144.aspx) e de [rajada Azure instâncias de trabalho com o Microsoft aos Pack](https://technet.microsoft.com/library/gg481749.aspx).

5. **Configurar um certificado de gestão na subscrição do Azure**

    Configure um certificado para proteger a ligação entre o nó cabeça e o Azure. Para opções e procedimentos, consulte o artigo [cenários para configurar o certificado de gestão do Azure para aos Pack](http://technet.microsoft.com/library/gg481759.aspx). Para implementações de teste, aos Pack instala um predefinido Microsoft aos Azure gestão de certificado rapidamente pode carregar para a sua subscrição do Azure.

6. **Criar um novo serviço de nuvem e uma conta de armazenamento**

    Utilize o portal clássico Azure para criar um serviço na nuvem e uma conta de armazenamento para a implementação numa região onde as instâncias RDMA noutros estão disponíveis.

7. **Criar um modelo de nó Azure**

    Utilizar o nó Assistente Criar modelo no HPC Cluster Manager. Para obter passos, consulte o artigo [criar um modelo de nó Azure](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) no "Passos para implementar o Azure nós aos Pack Microsoft".

    Para testes iniciais, sugerimos-lhe configurar uma política de disponibilidade manual no modelo.

8. **Adicionar nós ao cluster**

    Utilizar o Assistente de nó para adicionar no Gestor de clusters de HPC. Para mais informações, consulte o artigo [Adicionar o Azure nós ao cluster de do Windows](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).

    Quando especificar o tamanho de nós, selecione uma dos tamanhos de instância RDMA noutros.
    
    >[AZURE.NOTE]Cada rajada a implementação Azure com as instâncias com muitos cluster, aos Pack implementa automaticamente um mínimo de 2 instâncias RDMA noutros (como A8) como nós de proxy, para além das instâncias de função trabalhador Azure que especificar. Os nós de proxy utilizam núcleos que são atribuídos à subscrição e implicam taxas juntamente com as instâncias de função trabalhador Azure.

9. **Iniciar (aprovisionar) os nós e coloque-os online para executar tarefas**

    Selecione os nós e utilizar a ação **Iniciar** no HPC Cluster Manager. Quando aprovisiona o estiver concluída, selecione os nós e utilizar a ação **Colocar Online** no HPC Cluster Manager. Os nós estão prontos para executar tarefas.

10. **Submeter tarefas ao cluster**

    Utilize ferramentas de submissão de tarefa de aos Pack para executar tarefas de cluster. Consulte o artigo [Pack do Microsoft HPC: gestão de cargo](http://technet.microsoft.com/library/jj899585.aspx).

11. **Parar (deprovision) os nós**

    Quando terminar a execução de tarefas, colocar os nós offline e utilizar a ação **Parar** no HPC Cluster Manager.





## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Cenário 2. Implementar nós cluster com muitos cluster VMs (IaaS)

Neste cenário, implementar o nó de cabeça aos Pack e cluster calcular nós VMs associados a um domínio do Active Directory numa rede virtual Azure. AOS Pack fornece um número de [Opções de implementação no Azure VMs](virtual-machines-linux-hpcpack-cluster-options.md), incluindo scripts de implementação automatizada e modelos de guia de introdução Azure. Por exemplo, as considerações e passos abaixo orientá-lo para utilizar o [script de implementação aos Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para automatizar a maior parte deste processo.

![Cluster no Azure VMs][iaas]



### <a name="steps"></a>Passos

1. **Criar um nó de cabeça cluster e calcular nó VMs ao executar o script de implementação de aos Pack IaaS num computador cliente**

    Transferir o pacote de Script de implementação aos Pack IaaS a partir do [Centro de transferências da Microsoft](https://www.microsoft.com/download/details.aspx?id=49922).

    Para preparar o computador cliente, criar o ficheiro de configuração de script e executar o script, consulte o artigo [criar um Cluster de com o script de implementação aos Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). 
    
    Para implementar os nós de cluster RDMA noutros, tenha em atenção as seguintes considerações adicionais:
    
    * **Rede Virtual** - especificar uma nova rede virtual numa região em que o tamanho da instância RDMA noutros que pretende utilizar está disponível.

    * **Sistema operativo Windows Server** - para suportar a conectividade RDMA, especificar um sistema operativo Windows Server 2012 R2 ou Windows Server 2012 para o nó cluster VMs.

    * **Serviços em nuvem** - Recomendamos implementar o nó do seu cabeça num serviço de uma nuvem e os nós de cluster num serviço de nuvem diferente.

    * **Tamanho de nó de cabeça** - para este cenário, considere, pelo menos, um tamanho de A4 (Extra grande) para o nó cabeça.

    * **Extensão de HpcVmDrivers** - o script de implementação instala o agente de VM Azure e a extensão de HpcVmDrivers automaticamente quando implementar tamanho A8 ou A9 calcular nós com um sistema operativo Windows Server. HpcVmDrivers instala controladores no nó cluster VMs para que pode ligar-se para a rede RDMA.

    * **Configuração de rede de cluster** - o script de implementação configura automaticamente o cluster de pacote da topologia de 5 (todos os nós na rede da empresa). Esta topologia é necessária para todas as implementações de cluster aos Pack no VMs. Não são alterados a topologia da rede cluster mais tarde.

2. **Trazer os nós de cluster online para executar tarefas**

    Selecione os nós e utilizar a ação **Colocar Online** no HPC Cluster Manager. Os nós estão prontos para executar tarefas.

3. **Submeter tarefas ao cluster**

    Ligar para o nó de cabeça para submeter tarefas ou configurar um computador local para o fazer. Para obter informações, consulte o artigo [Submeter tarefas a um cluster HPC no Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).

4. **Colocar os nós offline e parar (retirar atribuição)-los**

    Quando terminar a execução de tarefas, tomar os nós offline no HPC Cluster Manager. Em seguida, utilize as ferramentas de gestão Azure para encerrá-los.



## <a name="run-mpi-applications-on-the-cluster"></a>Executar as aplicações de MPI no cluster

### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Exemplo: Executar mpipingpong num cluster aos Pack

Para verificar uma implementação Pack das instâncias RDMA noutros, execute o comando de **mpipingpong** aos Pack no cluster. **mpipingpong** envia os pacotes de dados entre nós emparelhado repetidamente para calcular a latência e medidas de débito e estatísticas de rede com capacidade de RDMA aplicação. Este exemplo mostra um padrão normal para a execução de uma tarefa MPI (neste caso, **mpipingpong**) utilizando o comando de **mpiexec** cluster.

Este exemplo assume que adicionou nós Azure numa configuração "rajada para Azure" ([cenário 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-(PaaS) in this article). Se implementado aos Pack num cluster de Azure VMs, terá de modificar a sintaxe de comando para especificar um grupo de nó diferente e definir variáveis de ambiente adicionais para direcionar tráfego de rede para a rede RDMA.


Para executar mpipingpong no cluster:


1. O nó cabeça ou num computador cliente adequadamente configurado, abra uma linha de comandos.

2. Para estimar latência entre pares de nós numa implementação do Azure rajada de 4 nós, escreva o seguinte comando para submeter uma tarefa para executar mpipingpong com um tamanho de pacote pequenas e um grande número de iterações:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    O comando devolve o ID da tarefa que é apresentado.

    Se tiver implementado cluster aos Pack implementado no Azure VMs, especifique um grupo de nó que contém calcular nó VMs implementados num serviço de nuvem único e modificar o comando **mpiexec** da seguinte forma:

    ```
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```

3. Quando a tarefa estiver concluída, para ver o resultado (neste caso, o resultado da tarefa 1 da tarefa), escreva o seguinte

    ```
    task view <JobID>.1
    ```

    onde &lt; *JobID* &gt; é o ID da tarefa que foi submetido.

    O resultado irá incluir resultados de latência semelhantes ao seguinte.

    ![Latência da pong ping][pingpong1]

4. Para estimar débito entre pares de nós do Azure rajada, escreva o seguinte comando para submeter uma tarefa para executar **mpipingpong** com um tamanho de pacote grandes e um pequeno número de iterações:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    O comando devolve o ID da tarefa que é apresentado.

    Num cluster aos Pack implementado no Azure VMs, modifique o comando conforme indicado no passo 2.

5. Quando a tarefa estiver concluída, para ver o resultado (neste caso, o resultado da tarefa 1 da tarefa), escreva o seguinte:

    ```
    task view <JobID>.1
    ```

  O resultado irá incluir resultados do débito semelhantes ao seguinte.

  ![Débito de pong ping][pingpong2]


### <a name="mpi-application-considerations"></a>Considerações sobre a aplicação MPI


Seguem-se considerações para executar aplicações do MPI com aos Pack no Azure. Alguns só se aplicam a implementações de nós do Azure (instâncias de função trabalhador adicionadas numa configuração "rajada para Azure").

* Instâncias de função de trabalho num serviço de nuvem são periodicamente reprovisioned sem aviso ao Azure (por exemplo, para a manutenção do sistema ou em caso de que falha de uma instância). Se uma instância é reprovisioned enquanto está a ser executada uma tarefa MPI, a instância perde os respetivos dados e regressa ao estado quando foi pela primeira vez implementado, que pode fazer com que a tarefa de MPI falhar. Os nós mais do que utilize para uma única tarefa MPI e quanto mais tempo a tarefa é executado, mais provável que um das instâncias irá sincronização será aprovisionado enquanto uma tarefa está em execução. Também deve considerar tal se designar um único nó na implementação como um servidor de ficheiros.


* Para executar tarefas MPI no Azure, não tem de utilizar as instâncias RDMA noutros. Pode utilizar qualquer tamanho instância que é suportado aos Pack. No entanto, as instâncias RDMA noutros lhe foram recomendadas relativamente em grande escala MPI tarefas que são sensíveis a latência e a largura de banda da rede que se liga os nós em execução. Se utilizar outros tamanhos para executar tarefas de MPI latência sensível e a largura de banda, recomendamos que execute pequenos trabalhos, na qual uma tarefa é executado em apenas alguns nós.

* Aplicações implementadas em instâncias Azure são sujeita aos termos de licenciamento associados à aplicação. Contacte o fornecedor de qualquer aplicação comercial para obter as restrições de licenciamento ou outras para executar na nuvem. Nem todos os fornecedores oferecem repartição licenciamento.


* Instâncias Azure necessita de configuração mais nós do access no local, partilhas e servidores de licenças. Por exemplo, para permitir que os nós Azure para aceder um servidor de licença no local, pode configurar uma rede de Azure virtual do site para o site.


* Para executar as aplicações de MPI em instâncias Azure, registe cada aplicação MPI Firewall do Windows nas instâncias ao executar o comando **hpcfwutil** . Esta opção permite-comunicações MPI realizar uma porta que é atribuída dinamicamente pela firewall.

    >[AZURE.NOTE] A rajada para implementações Azure, também pode configurar um comando de exceção de firewall para ser executada automaticamente em todos os nós Azure novos adicionado ao seu cluster. Depois de executar o comando **hpcfwutil** e confirme que funciona a aplicação, adicione o comando para um script de arranque para os nós do Azure. Para mais informações, consulte o artigo [utilizar um Script de arranque para nós Azure](https://technet.microsoft.com/library/jj899632.aspx).



* AOS Pack utiliza a variável de ambiente de cluster CCP_MPI_NETMASK para especificar um intervalo de endereços aceitáveis para a comunicação MPI. Iniciar aos Pack 2012 R2, a variável de ambiente de cluster CCP_MPI_NETMASK apenas afeta comunicação de MPI entre os nós de cluster de computação façam parte de um domínio (quer no local ou no Azure VMs). A variável é ignorada por nós adicionada numa rajada à configuração Azure.


* Não é possível executar tarefas MPI em várias instâncias Azure que são implementadas em serviços em nuvem diferente (por exemplo, numa rajada para implementações Azure com modelos de nó diferente ou nós de cluster Azure VM implementados em vários serviços em nuvem). Se tiver várias implementações do Azure nó que são iniciadas com modelos de nó diferente, a tarefa de MPI tem de executar no apenas um conjunto de nós Azure.


* Quando adicionar nós Azure ao seu cluster e mostrá-los online, o serviço de Programador de tarefas aos tentará imediatamente iniciar tarefas em nós. Se apenas uma parte da sua carga de trabalho pode ser executados em Azure, certifique-se de que atualizar ou criar modelos de tarefa para definir o que tipos de tarefas podem ser executados em Azure. Por exemplo, para garantir que apenas executar tarefas submetidas com um modelo de tarefa no Azure nós, adicionar a propriedade de nó grupos para o modelo de tarefa e selecione AzureNodes como o valor pretendido. Para criar grupos personalizados para os nós do Azure, utilize o cmdlet do PowerShell de aos HpcGroup adicionar.


## <a name="next-steps"></a>Próximos passos

* Como alternativa à utilização do pacote de aos, desenvolva com o serviço do Azure lote executar MPI aplicações em agrupamentos de gerida de nós do cluster no Azure. Consulte o artigo [tarefas com várias instâncias de utilização para executar as aplicações de mensagem prisma Interface (MPI) no lote Azure](../batch/batch-mpi.md).

* Se pretender executar Linux MPI aplicações que acedam à rede do Azure RDMA, consulte o artigo [configurar um cluster de Linux RDMA para executar MPI aplicações](virtual-machines-linux-classic-rdma-cluster.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/burst.png
[iaas]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/iaas.png
[pingpong1]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong2.png