<properties
 pageTitle="Adicionar nós de rajada a um cluster de pacote aos | Microsoft Azure"
 description="Saiba como expandir um cluster de aos Pack no Azure a pedido adicionando instâncias de função de trabalho em execução num serviço de nuvem"
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
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Adicionar a pedido "rajada" nós a um cluster de aos Pack no Azure



Se configurar um cluster de [Pacote de do Microsoft](https://technet.microsoft.com/library/cc514029) Azure, poderá querer uma forma para dimensionar rapidamente a capacidade de cluster para cima ou para baixo, sem manter um conjunto de cluster pré-configurado nó VMs. Este artigo mostra-lhe como adicionar a pedido "rajada" nós (instâncias de função de trabalho em execução num serviço de nuvem) como recursos de cluster para um nó cabeça no Azure. 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

![Nós de rajada][burst]

Os passos neste artigo ajudá-lo adicionar nós Azure rapidamente a um baseado na nuvem aos Pack cabeça nó VM de um teste ou prova de conceito implementação. Os passos de alto nível são o mesmo que os passos para "rajada para Azure" para adicionar na nuvem calcular capacidade a um cluster de aos Pack no local. Para obter um tutorial, consulte o artigo [configurar um híbrido calcular cluster com o Microsoft aos Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Para orientação detalhada e considerações para implementações de produção, consulte o artigo [rajada para Azure com o Microsoft aos Pack](https://technet.microsoft.com/library/gg481749.aspx).


## <a name="prerequisites"></a>Pré-requisitos

* **Nó de cabeça aos Pack implementado numa VM Azure** - pode utilizar um nó de cabeça autónomo VM ou uma que faz parte de um cluster de maior. Para criar um nó de cabeça autónomo, consulte o artigo [Implementar um nó de cabeça Pack numa VM Azure](virtual-machines-windows-hpcpack-cluster-headnode.md). Para opções de implementação do cluster de pacote aos automatizado, consulte o artigo [Opções para criar e gerir um cluster de do Windows Azure com o Microsoft aos Pack](virtual-machines-windows-hpcpack-cluster-options.md).

    >[AZURE.TIP] Se utilizar o [script de implementação aos Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para criar o cluster no Azure, pode incluir nós de rajada Azure na sua implementação automatizada. Consulte os exemplos no artigo.

* **Subscrição do azure** - adicionar nós Azure, pode escolher a mesma subscrição utilizado para implementar o nó cabeça VM, ou uma subscrição diferente (ou subscrições).

* **Quota núcleos** - é possível que tenha aumentar a quota de núcleos, especialmente se optar por implementar vários nós Azure com tamanhos multicore. Para aumentar uma quota, [Abra um pedido de suporte online do cliente](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sem encargos.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Passo 1: Criar um serviço na nuvem e uma conta de armazenamento para os nós do Azure

Utilizar o portal do Azure clássico ou ferramentas equivalentes para configurar os seguintes recursos que são necessários para implementar os nós do Azure:

* Um novo serviço de nuvem Azure
* Uma nova conta de armazenamento Azure

>[AZURE.NOTE] Não reutilize um serviço de nuvem existente na sua subscrição. 

**Considerações sobre**

* Configure um serviço em nuvem separada para cada modelo de nó Azure que planeia criar. No entanto, pode utilizar a mesma conta de armazenamento para vários modelos de nó.

* Recomendamos que localizar o serviço em nuvem e a conta de armazenamento para a implementação na mesma região Azure.




## <a name="step-2-configure-an-azure-management-certificate"></a>Passo 2: Configurar um certificado de gestão do Azure

Para adicionar nós Azure como recursos de cluster, é necessário um certificado de gestão do nó cabeça e carregar um correspondente certificado para a subscrição Azure utilizada para a implementação.

Para este cenário, pode escolher o **Certificado de gestão de Azure aos predefinido** aos Pack instala e configura automaticamente no nó cabeça. Este certificado é útil para testar os efeitos e prova de conceito híbridas. Para utilizar este certificado, carregue o ficheiro C:\Program Files\Microsoft aos Pack 2012\Bin\hpccert.cer a partir do nó cabeça VM para a subscrição. Para carregar o certificado no [Azure portal clássico](https://manage.windowsazure.com), clique em **Definições** > **Certificados de gestão**.

Para obter opções adicionais configurar o certificado de gestão, consulte o artigo [cenários para configurar o certificado de gestão do Azure para implementações de rajada Azure](http://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Passo 3: Implementar nós Azure ao cluster



Os passos para adicionar e iniciar o Azure nós neste cenário são geralmente o mesmo que os passos com um nó de cabeça no local. Para obter mais informações, consulte as secções seguintes [passos para implementar o Azure nós com o Microsoft aos Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Criar um modelo de nó Azure

* Adicionar nós Azure ao cluster do Windows

* Iniciar nós (aprovisionar) o Azure

Depois de adicionar e iniciar os nós, está prontos utilizar para executar tarefas de cluster.

Se tiver problemas quando implementar o Azure nós, consulte o artigo [Resolver problemas de implementações do Azure nós com o Microsoft aos Pack](http://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>Próximos passos

* Para utilizar um tamanho de instância com muitos cluster para os nós de rajada, consulte o artigo considerações em [sobre H série e com muitos cluster VMs A série](virtual-machines-windows-a8-a9-a10-a11-specs.md).

* Se pretender aumentar ou diminuir os recursos informáticos Azure de acordo com a carga de trabalho cluster automaticamente, consulte o artigo [automaticamente aumentar e diminuir Azure cluster recursos num cluster aos Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-cluster-node-burst/burst.png
