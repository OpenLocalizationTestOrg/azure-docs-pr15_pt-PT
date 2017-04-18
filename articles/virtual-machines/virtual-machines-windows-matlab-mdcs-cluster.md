<properties
   pageTitle="MATLAB clusters em máquinas virtuais | Microsoft Azure"
   description="Utilizar máquinas virtuais do Microsoft Azure para criar clusters MATLAB distribuído Computing Server para executar o seu cluster-a com um grau elevado paralelas MATLAB das cargas de trabalho"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mscurrell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Windows"
   ms.workload="infrastructure-services"
   ms.date="05/09/2016"
   ms.author="markscu"/>

# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Criar clusters de servidor de computação distribuída da MATLAB no Azure VMs 

Utilize máquinas virtuais do Microsoft Azure para criar um ou mais clusters MATLAB distribuído Computing Server para executar o seu cluster-a com um grau elevado paralelo MATLAB das cargas de trabalho. Instale o software de servidor de computação distribuída da MATLAB numa VM para utilizar como uma imagem base e utilizar um modelo de guia de introdução Azure ou script Azure PowerShell (disponível no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) para implementar e gerir o cluster. Após a implementação, ligar ao cluster para executar a sua das cargas de trabalho. 

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Sobre MATLAB e MATLAB distribuído computação Server 

A plataforma [MATLAB](http://www.mathworks.com/products/matlab/) está optimizada para resolução de problemas de engenharia e científicos. MATLAB os utilizadores com simulações em grande escala e tarefas de processamento de dados podem utilizar paralelamente MathWorks computação produtos para acelerar as cargas de trabalho com muitos cluster tirando partido da cluster clusters e serviços de grelha. [Caixa de ferramentas de computação paralelas](http://www.mathworks.com/products/parallel-computing/) permite aos utilizadores MATLAB parallelize aplicações e tirar partido de vários core processadores, GPUs e calcular clusters. [Servidor de computação distribuída da MATLAB](http://www.mathworks.com/products/distriben/) permite aos utilizadores MATLAB utilizam vários computadores num cluster de computação. 


Ao utilizar máquinas virtuais Azure, pode criar clusters de MATLAB distribuído servidor computação que tenham as mesmas mecanismos disponíveis para submeter trabalho paralelo como clusters no local, tal como tarefas interativas, trabalhos em lotes, tarefas independentes e comunicar tarefas. Utilizar o Azure em conjunto com a plataforma MATLAB tem várias vantagens em comparação com aprovisionamento e utilizar tradicional no local hardware: tamanhos de um intervalo de máquina virtual, criação de clusters a pedido para pagar apenas para os recursos de cluster utilização e a capacidade para testar os modelos de escala.  

## <a name="prerequisites"></a>Pré-requisitos

* **Computador cliente** - terá de um computador cliente baseados no Windows para comunicar com o Azure e cluster de MATLAB distribuído computação servidor após a implementação. 

* **Azure PowerShell** - consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) instalá-lo no computador cliente. 

* **Subscrição do azure** - se não tiver uma subscrição, pode criar um [livre conta](https://azure.microsoft.com/free/) apenas de duas minutos. Para clusters de maiores dimensões, considere uma subscrição repartição ou outras opções de compra. 

* **Quota núcleos** - é possível que tenha aumentar a quota de core para implementar um cluster de grande ou mais do que um cluster de servidor de computação distribuída da MATLAB. Para aumentar uma quota, [Abra um pedido de suporte online do cliente](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sem encargos. 

* **MATLAB, paralelas computação caixa de ferramentas e licenças de servidor de computação distribuída da MATLAB** - os scripts partem do princípio de que o [Gestor de licenças alojado MathWorks](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) é utilizado para todas as licenças.  

* **Software de servidor de computação distribuída da MATLAB** - será instalado numa VM que será utilizada como a imagem VM base para o cluster VMs. 


## <a name="high-level-steps"></a>Alto passos nível

Para utilizar o Azure máquinas virtuais seu MATLAB distribuído computação para clusters de servidor, são necessários os seguintes passos de alto nível. Obter instruções detalhadas são na documentação que acompanham o modelo de guia de introdução e scripts no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Criar uma imagem VM base**  
    * Transfira e instale o software de servidor de computação distribuída da MATLAB para este VM. 

    >[AZURE.NOTE]Este processo pode demorar algumas horas, mas só tem de fazê-lo, uma vez para cada versão do MATLAB utilizar.   
    
2. **Criar um ou mais clusters**  
    * Utilize o script do PowerShell fornecido ou utilize o modelo de guia de introdução para criar um cluster da imagem VM base.   
    * Gerir clusters utilizando o script PowerShell fornecido que lhe permite da lista, coloque o cursor, retomar e eliminar clusters. 
 
## <a name="cluster-configurations"></a>Configurações de cluster 

Atualmente, o modelo e script de criação de cluster permitem-lhe criar uma topologia de servidor de computação distribuída da MATLAB única. Se pretender, criar um ou mais clusters adicionais, com cada cluster de um número diferente de trabalho VMs, com diferentes tamanhos VM e assim sucessivamente. 

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB cliente e cluster no Azure 

O nó do cliente MATLAB, nó MATLAB Programador de tarefas e nós de servidor de computação distribuída da MATLAB "da civil" estão todas configurados como Azure VMs numa rede virtual, conforme apresentado na figura seguinte. 

![Cluster topologia](./media/virtual-machines-windows-matlab-mdcs-cluster/mdcs_cluster.png)

* Para utilizar o cluster, ligar-se ao ambiente de trabalho remoto para o nó do cliente. O nó do cliente é executado o cliente MATLAB. 

* O nó do cliente tem uma partilha de ficheiros que pode ser acedida por todos os trabalhadores.

* Gestor de licenças alojado MathWorks é utilizado para os controlos de licença para todo o software MATLAB. 

* Por predefinição, é criado um trabalho de servidor de computação distribuída da MATLAB por core no trabalhador VMs, mas pode especificar qualquer número. 


## <a name="use-an-azure-based-cluster"></a>Utilizar um Cluster baseadas no Azure 

Como com outros tipos de servidor de computação distribuída da MATLAB clusters, tem de utilizar o Gestor de perfil de Cluster no cliente do MATLAB (no cliente VM) para criar um perfil de cluster MATLAB Programador de tarefas.

![Gestor de clusters de perfil](./media/virtual-machines-windows-matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Próximos passos

* Para obter instruções detalhadas implementar e gerir clusters de servidor de computação distribuída da MATLAB no Azure, consulte o artigo o repositório de [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) que contém os modelos e scripts. 

* Aceda ao [MathWorks site](http://www.mathworks.com/) para obter documentação detalhada para MATLAB e MATLAB distribuído computação servidor.
