<properties
 pageTitle="Criar um nó de cabeça aos Pack numa VM Azure | Microsoft Azure"
 description="Saiba como utilizar o portal do Azure e o modelo de implementação do Gestor de recursos para criar um nó de cabeça Microsoft aos Pack numa VM Azure."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/17/2016"
 ms.author="danlep"/>

# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Criar o nó cabeça de um cluster de aos Pack numa VM Azure com uma imagem em Marketplace


Utilize uma [imagem de máquina virtual Microsoft aos Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) a partir do Azure Marketplace e o Azure portal para criar o nó cabeça de um cluster de HPC. Esta imagem aos Pack VM é baseada no Windows Server 2012 R2 Centro de dados com aos Pack 2012 R2 atualização 3 pré-instalado. Utilize este nó de cabeça para uma prova de implementação de conceito aos Pack no Azure. Em seguida, pode adicionar cluster nós ao cluster para executar HPC cargas de trabalho.



>[AZURE.TIP]Para implementar um cluster de pacote aos concluída no Azure que inclui o nó cabeça e nós de cluster, recomendamos que utilize um método automatizado. Opções incluem o [script de implementação aos Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) e o modelo de Gestor de recursos de [cluster de aos Pack para das cargas de trabalho do Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/) . Consulte o artigo [Opções do pacote de aos cluster no Azure](virtual-machines-windows-hpcpack-cluster-options.md) modelos adicionais. 


## <a name="planning-considerations"></a>Considerações de planeamento

Como apresentado na figura seguinte, implemente a nó cabeça aos Pack um domínio do Active Directory numa rede virtual Azure.

![Nó de cabeça aos Pack][headnode]

* **Domínio do active Directory** - o aos Pack nó cabeça deve estar associado a um domínio do Active Directory no Azure antes de iniciar os serviços HPC na VM. Como é mostrado neste artigo, para uma prova de implementação de conceito, pode promover a VM criar para o nó cabeça como um controlador de domínio antes de iniciar os serviços HPC. Outra opção é implementar um controlador de domínio separado e floresta no Azure ao qual participa o nó cabeça VM.

* **Azure rede virtual** - ao utilizar o modelo de implementação do Gestor de recursos para implementar o nó cabeça, especifique ou criar uma rede virtual Azure. Utilizar a rede virtual se precisar de participar o nó de cabeça para um domínio do Active Directory existente. Também precisa de mais tarde para adicionar cluster nó VMs ao cluster.

    
## <a name="steps-to-create-the-head-node"></a>Passos para criar o nó cabeça

Seguem-se os passos de alto nível para utilizar o portal do Azure para criar um VM Azure para o nó de cabeça aos Pack utilizando o modelo de implementação do Gestor de recursos. 


1. Se pretender criar uma nova floresta do Active Directory no Azure com controlador de domínio separado VMs, é uma opção utilizar um [modelo de Gestor de recursos](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/). Para uma prova simple de implementação de conceito, é fino omitir este passo e configure o nó cabeça VM propriamente dito como um controlador de domínio. Esta opção é descrita neste artigo.
    
2. No [Aos Pack 2012 R2 na página do Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) no Azure Marketplace, clique em **Criar Máquina Virtual**. 

3. No portal do, na página **Aos Pack 2012 R2 no Windows Server 2012 R2** , selecione o modelo de implementação do **Gestor de recursos** e, em seguida, clique em **Criar**.

    ![Imagem aos Pack][marketplace]

4. Utilize o portal para configurar as definições e criar a VM. Se estiver familiarizado com o Azure, siga o tutorial [criar uma máquina de virtual do Windows no portal do Azure](virtual-machines-windows-hero-tutorial.md). Para uma prova de implementação de conceito, normalmente pode aceitar a predefinição ou definições recomendadas.

    >[AZURE.NOTE]Se pretende aderir o nó de cabeça para um domínio do Active Directory existente no Azure, certifique-se de que especificar a rede virtual para esse domínio ao criar a VM.
       
4. Após criar a VM e a VM está a ser executado, [ligar-se para a VM](virtual-machines-windows-connect-logon.md) ao ambiente de trabalho remoto. 

5. Aderir a VM a uma floresta domínio existente ou criar floresta de um domínio a VM própria.

    * Se criou a VM numa rede virtual Azure com floresta domínio existente, aderir a VM à floresta utilizando ferramentas padrão do Gestor de servidor ou o Windows PowerShell. Em seguida, reinicie.

    * Se criou a VM numa nova rede virtual (sem floresta domínio existente), em seguida, promova a VM como um controlador de domínio. Siga passos padrão para instalar e configurar a função do Active Directory Domain Services no nó cabeça. Para obter passos detalhados, consulte o artigo [instalar uma nova Windows Server 2012 Active Directory floresta](https://technet.microsoft.com/library/jj574166.aspx).

5. Depois da VM está a ser executado e está associada a uma floresta do Active Directory, inicie os serviços de aos Pack da seguinte forma:

    um. Ligar para o nó cabeça VM utilizando uma conta do domínio que é um membro do grupo de administradores local. Por exemplo, utilize a conta de administrador que configurar quando criou o nó cabeça VM.

    b. Para uma configuração de cabeça nó predefinida, inicie o Windows PowerShell como administrador e escreva o seguinte:

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Pode demorar alguns minutos para que os serviços de aos Pack começar.

    Para opções de configuração de nó de cabeça adicional, escreva `get-help HPCHNPrepare.ps1`.


## <a name="next-steps"></a>Próximos passos

* Agora pode trabalhar com o nó cabeça do seu cluster aos Pack. Por exemplo, iniciar o Gestor de Cluster HPC e concluir a [Lista de tarefas de implementação](https://technet.microsoft.com/library/jj884141.aspx).
* Se pretender aumentar o cluster calcular capacidade a pedido, adicione [nós de rajada Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md) num serviço de nuvem. 

* Tente executar uma carga de trabalho de teste no cluster. Por exemplo, consulte o artigo o pacote aos [Guia de introdução](https://technet.microsoft.com/library/jj884144).

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png
