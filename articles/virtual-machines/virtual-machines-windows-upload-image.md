<properties
    pageTitle="Carregar um VHD do Windows para o Gestor de recursos | Microsoft Azure"
    description="Saiba como carregar uma Windows máquina virtual VHD nos locais para Azure, utilizando o modelo de implementação do Gestor de recursos. Pode carregar um VHD a partir do quer um GRG ou uma VM especializada."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>

# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>Carregar um VHD a partir de um VM no local para Azure do Windows 


Este artigo mostra-lhe como criar e carregar um Windows virtual no disco rígido (VHD) para ser utilizado na criação de um Vm Azure. Pode carregar um VHD a partir de uma VM GRG ou uma VM especializada. 

Para obter mais detalhes sobre discos e VHDs no Azure, consulte o artigo [sobre discos e VHDs para máquinas virtuais](virtual-machines-linux-about-disks-vhds.md).


## <a name="prepare-the-vm"></a>Preparar a VM 

Pode carregar VHDs GRG e especializados para Azure. Cada tipo requer que preparar a VM antes do início.

- **GRG VHD** - um VHD GRG teve todas as suas informações de conta pessoal removidas utilizando Sysprep. Se pretender utilizar o VHD como uma imagem para criar VMs novos a partir de, deverá:
    - [Preparar um VHD do Windows para carregar para o Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). 
    - [Generalize máquina virtual utilizando o Sysprep](virtual-machines-windows-generalize-vhd.md). 

- **VHD especializadas** - um VHD especializado mantém as contas de utilizador, aplicações e outros dados de estado a partir do seu VM original. Se pretender utilizar o VHD como-é criar uma nova VM, certifique-se os passos seguintes são concluídos. 
    - [Preparar um VHD do Windows para carregar para o Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). **Não faça** generalizar VM utilizando o Sysprep.
    - Remova qualquer ferramentas de Virtualização convidado e agentes que são instalados na VM (ou seja, ferramentas VMware).
    - Certifique-se de que a VM está configurada para separar o seu endereço IP e definições de DNS através de DHCP. Este procedimento assegura que o servidor obtém um endereço IP dentro de VNet ao arrancar. 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Se ainda não tiver PowerShell versão 1.4 ou acima instaladas, saiba [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

1. Abra o PowerShell do Azure e inicie sessão na sua conta Azure. É aberta uma janela de pop-up para introduzir as credenciais da conta Azure.

    ```powershell
    Login-AzureRmAccount
    ```


2. Obter a subscrição IDs para as suas subscrições disponíveis.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Definir a subscrição correta utilizando o ID da subscrição. Substituir `<subscriptionID>` com o ID da subscrição do correto.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Obter a conta de armazenamento

É necessária uma conta de armazenamento no Azure para armazenar a imagem VM carregada. Pode utilizar uma conta de armazenamento existente ou crie um novo. 

Para mostrar as contas de armazenamento disponível, escreva:

```powershell
Get-AzureRmStorageAccount
```

Se pretender utilizar uma conta existente do armazenamento, avance para a secção [carregar a imagem VM](#upload-the-vm-vhd-to-your-storage-account) .

Se necessitar de criar uma conta de armazenamento, siga estes passos:

1. Precisa do nome do grupo de recursos onde deve ser criada a conta de armazenamento. Para saber todos os grupos de recursos que estão na sua subscrição, escreva:

    ```powershell
    Get-AzureRmResourceGroup
    ```

Para criar um grupo de recursos com o nome **myResourceGroup** na região **Ocidental dos EUA** , escreva:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Crie uma conta de armazenamento denominada **mystorageaccount** neste grupo de recursos utilizando o cmdlet [AzureRmStorageAccount novo](https://msdn.microsoft.com/library/mt607148.aspx) :

    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" -SkuName "Standard_LRS" -Kind "Storage"
    ```
            
    Valores válidos para - SkuName são:

    - **Standard_LRS** - armazenamento localmente redundante. 
    - **Standard_ZRS** - armazenamento redundante da zona.
    - **Standard_GRS** - Geo redundante armazenamento. 
    - **Standard_RAGRS** - acesso de leitura geo redundante armazenamento. 
    - **Premium_LRS** - armazenamento localmente redundante Premium. 



## <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD à sua conta de armazenamento

Utilize o cmdlet [Adicionar AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) para carregar a imagem a um contentor na sua conta de armazenamento. Este exemplo carrega o ficheiro **myVHD.vhd** a partir do `"C:\Users\Public\Documents\Virtual hard disks\"` ao armazenamento de um conta com o nome **mystorageaccount** no grupo de recursos **myResourceGroup** . O ficheiro será colocado para o contentor **mycontainer** com o nome e o novo nome de ficheiro serão **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se tiver êxito, obtém uma resposta com um aspeto semelhante ao seguinte:

```
  C:\> Add-AzureRmVhd -ResourceGroupName myResourceGroup -Destination https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
  MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
  MD5 hash calculation is completed.
  Elapsed time for the operation: 00:03:35
  Creating new page blob of size 53687091712...
  Elapsed time for upload: 01:12:49

  LocalFilePath           DestinationUri
  -------------           --------------
  C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Dependendo da sua ligação de rede e o tamanho do seu ficheiro VHD, este comando poderá demorar algum tempo para concluir


## <a name="next-steps"></a>Próximos passos

- [Criar uma VM no Azure a partir de um VHD GRG](virtual-machines-windows-create-vm-generalized.md)
- [Criar uma VM no Azure a partir de um VHD especializado](virtual-machines-windows-create-vm-specialized.md) por anexá-la como um disco SO quando cria uma nova VM.


