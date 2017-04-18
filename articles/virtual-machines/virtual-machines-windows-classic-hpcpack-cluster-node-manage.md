<properties
 pageTitle="Gerir nós de cluster de computação aos Pack | Microsoft Azure"
 description="Saiba mais sobre as ferramentas de script do PowerShell para adicionar, remover, iniciar e parar nós de cluster de cluster aos Pack no Azure"
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

# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Gerir o número e a disponibilidade de nós do cluster num cluster aos Pack no Azure

Se tiver criado um cluster de aos Pack no Azure VMs, poderá querer formas de facilmente adicionar, remover, iniciar (aprovisionar) ou parar de nó (deprovision) um número de cluster VMs do cluster. Para efetuar estas tarefas, execute scripts de Azure PowerShell que são instaladas no nó cabeça VM. Estes scripts ajudá-lo a controlar a número e a disponibilidade dos seus recursos de cluster aos Pack para poder controlar os custos.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>Pré-requisitos

* **Cluster aos Pack no Azure VMs** - criar um cluster de aos Pack no modelo de implementação clássica com, pelo menos, aos Pack 2012 R2 atualização 1. Por exemplo, pode automatizar a implementação utilizando a imagem aos Pack VM atual no Azure Marketplace e um script do Azure PowerShell. Para informações e pré-requisitos, consulte o artigo [criar um Cluster de com o script de implementação aos Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

    Após a implementação, localizar o nó scripts de gestão nos campos % CCP\_base % posição pasta o nó cabeça. Tem de executar cada um dos scripts como administrador.

* **Azure publicar as definições de ficheiro ou gestão de certificados** - que tem de fazer um dos seguintes procedimentos no nó cabeça:

    * **Importar o Azure publicar o ficheiro de definições**. Para fazer isto, execute os seguintes cmdlets do Azure PowerShell no nó cabeça:

    ```
    Get-AzurePublishSettingsFile

    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```

    * **Configurar o certificado de gestão Azure no nó cabeça**. Se tiver o ficheiro. cer, importá-lo no arquivo de certificados CurrentUser\My e, em seguida, execute o seguinte cmdlet do Azure PowerShell no seu ambiente do Azure (AzureCloud ou AzureChinaCloud):

    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Adicionar cluster nó VMs

Adicione nós de cluster com o script **HpcIaaSNode.ps1 adicionar** .

### <a name="syntax"></a>Sintaxe
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Parâmetros

* **ServiceName** - nome na nuvem serviço nesse nó cluster novo VMs serão adicionados à.

* **Nomeimagem** - nome da imagem Azure VM, que pode ser obtido através do portal clássico Azure ou Azure PowerShell cmdlet **Get-AzureVMImage**. A imagem tem de cumprir os seguintes requisitos:

    1. Tem de estar instalado um sistema operativo Windows.

    2. Tem de estar instalado o pacote de na função nó cluster.

    3. A imagem tem de ser uma imagem privada na categoria de utilizador, não uma imagem do Azure VM pública.

* **Quantidade** - número de cluster nó VMs seja adicionada.

* **InstanceSize** - tamanho do nó cluster VMs.

* **DomainUserName** - nome de utilizador do domínio, que será utilizada para aderir as VMs novos para o domínio.

* **DomainUserPassword** - palavra-passe do utilizador do domínio.

* **NodeNameSeries** (opcional) - nomenclatura padrão para os nós de cluster. O formato deve ser &lt; *raiz\_nome*&gt;&lt;*Iniciar\_número*&gt;%. Por exemplo, MyCN % 10% meios de uma série dos nomes de nó cluster começando MyCN11. Se não for especificado, o script utiliza o nó configurado atribuir nomes a série de HPC cluster.

### <a name="example"></a>Exemplo

O exemplo seguinte adiciona nó de cluster de grande tamanho 20 VMs a nuvem serviço *hpcservice1*, com base na imagem VM *hpccnimage1*.

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Remover cluster nó VMs

Remova nós de cluster com o script **HpcIaaSNode.ps1 remover** .

### <a name="syntax"></a>Sintaxe

```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Parâmetros

* **Nome** - nomes de nós de cluster para ser removido. Carateres universais são suportadas. O nome do conjunto de parâmetro é o nome. Não é possível especificar o **nome** e o **nó** parâmetros.

* **Nó** - HpcNode o objeto para os nós ser removido, que pode ser obtido através do cmdlet do PowerShell de aos [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). O nome do conjunto de parâmetro é nó. Não é possível especificar o **nome** e o **nó** parâmetros.

* **DeleteVHD** (opcional) - a definição para eliminar os discos associados para os VMs que são removidos.

* **Forçar** (opcional) - a definição para forçar nós HPC offline antes de removê-los.

* **Confirmar** (opcional) - pedir confirmação antes de executar o comando.

* **WhatIf** - definição para descrever o que deverá acontecer se executou o comando sem realmente, a executar o comando.

### <a name="example"></a>Exemplo

O exemplo seguinte força nós offline com nomes comecem *HPCNode-CN -* e -los remove os nós e os seus discos associados.

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Iniciar cluster nó VMs

Inicie o cluster nós com o script **HpcIaaSNode.ps1 iniciar** .

### <a name="syntax"></a>Sintaxe

```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Parâmetros

* **Nome** - os nomes de nós do cluster para ser iniciado. Carateres universais são suportadas. O nome do conjunto de parâmetro é o nome. Não é possível especificar o **nome** e o **nó** parâmetros.

* **Nó**- HpcNode o objeto para os nós ser iniciado, que pode ser obtido através do cmdlet do PowerShell de aos [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). O nome do conjunto de parâmetro é nó. Não é possível especificar o **nome** e o **nó** parâmetros.

### <a name="example"></a>Exemplo

O exemplo seguinte é iniciado nós com nomes comecem *HPCNode-CN -*.

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Parar de cluster nó VMs

Pare de nós de cluster com o script **HpcIaaSNode.ps1 parar** .

### <a name="syntax"></a>Sintaxe

```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Parâmetros


* **Nome**- os nomes de nós do cluster a ser parado. Carateres universais são suportadas. O nome do conjunto de parâmetro é o nome. Não é possível especificar o **nome** e o **nó** parâmetros.

* **Nó** - HpcNode o objeto para os nós a ser parado, que pode ser obtido através do cmdlet do PowerShell de aos [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). O nome do conjunto de parâmetro é nó. Não é possível especificar o **nome** e o **nó** parâmetros.

* **Forçar** (opcional) - a definição para forçar nós HPC offline antes de pará-los.

### <a name="example"></a>Exemplo

O exemplo seguinte força nós offline com nomes comecem *HPCNode-CN -* e, em seguida, deixa os nós.

Parar-HPCIaaSNode.ps1 – nome HPCNodeCN-*-força

## <a name="next-steps"></a>Próximos passos

* Se pretender que uma forma para aumentar ou diminuir os nós de cluster de acordo com a carga de trabalho atual das tarefas e tarefas no cluster automaticamente, consulte o artigo [automaticamente aumentar e diminuir os recursos de cluster aos Pack no Azure de acordo com a carga de trabalho de cluster](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).
