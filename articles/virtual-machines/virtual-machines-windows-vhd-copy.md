<properties
    pageTitle="Criar uma cópia de uma VM especializada no Azure | Microsoft Azure"
    description="Saiba como criar uma cópia de um VM Windows especializadas em execução no Azure, no modelo de implementação de Gestor de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>Criar uma cópia de um VM Windows especializadas em execução no Azure 

Este artigo mostra-lhe como utilizar a ferramenta de AZCopy para criar uma cópia do VHD a partir de uma especializadas VM Windows que está a ser executado no Azure. Em seguida, pode utilizar a cópia do VHD para criar uma nova VM. 

- Se pretende copiar uma VM GRG, veja [como criar uma imagem VM a partir de um VM do Azure GRG existente](virtual-machines-windows-capture-image.md).

- Se pretende carregar um VHD a partir de um VM no local, como um criada utilizando o Hyper-V, consulte [carregar um VHD Windows a partir de um VM no local para Azure](virtual-machines-windows-upload-image.md).


## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que:

- Contêm informações acerca de **contas de armazenamento de origem e destino**. Para a origem VM, tem de nomes de conta e o contentor de armazenamento. Normalmente, o nome do contentor serão **vhds**. Também tem de ter uma conta de armazenamento de destino. Se ainda não tiver uma, pode criar um utilizando o portal de um dos (**Mais serviços** > contas de armazenamento > Adicionar) ou utilizando o cmdlet [AzureRmStorageAccount novo](https://msdn.microsoft.com/library/mt607148.aspx) . 

- Têm o Azure [PowerShell 1.0](../powershell-install-configure.md) (ou posterior) instalado.

- Transferiu e instalou a [ferramenta de AzCopy](../storage/storage-use-azcopy.md). 


## <a name="deallocate-the-vm"></a>Retirar a VM

Retirar a VM, que liberta VHD sejam copiados. 

- **Portal**: clique em **máquinas virtuais** > **myVM** > parar
- **PowerShell**: `Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM` retira VM denominada **myVM** no grupo de recursos **myResourceGroup**a atribuição.

O **Estado** para a VM no portal do Azure altera de **parado** para **parado (desatribuído)**.


## <a name="get-the-storage-account-urls"></a>Obter os URLs de conta de armazenamento

Tem os URLs das contas de armazenamento de origem e destino. O aspeto de URLs: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Se já souber o nome de conta e o contentor de armazenamento, pode substituir apenas as informações entre parênteses para criar o seu URL. 

Pode utilizar o Azure portal ou Azure Powershell para obter o URL:

- **Portal**: clique em **mais serviços** > **contas de armazenamento**  >  <storage account> **Blobs** e ficheiro de origem do VHD é provavelmente no contentor **vhds** . Clique em **Propriedades** para o contentor e copiar o texto denominado **URL**. Terá os URLs das contentores de origem e de destino. 

- **PowerShell**: `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` obtém as informações para VM denominada **myVM** no de grupo de recursos **myResourceGroup**. Nos resultados, procure o **Vhd Uri**na secção de **perfil de armazenamento** . A primeira parte do Uri é o URL para o contentor e a última parte é o nome do sistema operativo VHD para a VM.

## <a name="get-the-storage-access-keys"></a>Obter as teclas de acesso de armazenamento

Localize as teclas de acesso para a origem e destino contas de armazenamento. Para mais informações acerca das teclas de acesso, consulte o artigo [sobre Azure contas de armazenamento](../storage/storage-create-storage-account.md).

- **Portal**: clique em **mais serviços** > **contas de armazenamento**  >  <storage account> **Todas as definições de** > **teclas de acesso**. Copie a chave assinalada como **chave1**.
- **PowerShell**: `Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup` obtém a chave de armazenamento para a conta de armazenamento **mystorageaccount** no de grupo de recursos **myResourceGroup**. Copie a chave assinalada como **chave1**.


## <a name="copy-the-vhd"></a>Copie o VHD 

Pode copiar ficheiros entre contas de armazenamento utilizando AzCopy. Para o contentor de destino, se não existir contentor especificado, será criado por si. 

Para utilizar AzCopy, abra uma linha de comandos no computador local e navegue para a pasta onde o AzCopy está instalado. Será semelhante a *c:\Programas\Microsoft ficheiros (x86) \Microsoft SDKs\Azure\AzCopy*. 

Para copiar todos os ficheiros de um contentor, utilize o parâmetro **/S** . Isto pode ser utilizado para copiar o VHD SO e todos os dados de discos se estiverem no mesmo contentor. Este exemplo mostra como copiar todos os ficheiros no contentor **mysourcecontainer** na conta de armazenamento **mysourcestorageaccount** para o contentor **mydestinationcontainer** na conta de armazenamento **mydestinationstorageaccount** . Substitua os nomes das contas de armazenamento e contentores pela sua própria. Substituir `<sourceStorageAccountKey1>` e `<destinationStorageAccountKey1>` com as suas próprias teclas.

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Se apenas quiser copiar um VHD específico num contentor com vários ficheiros, também pode especificar o nome do ficheiro com o parâmetro /Pattern. Neste exemplo, apenas o ficheiro com o nome **myFileName.vhd** será copiado.

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
        /Pattern:myFileName.vhd
```


Quando terminar, irá obter uma mensagem que tem um aspeto semelhante:

```
  Finished 2 of total 2 file(s).
  [2016/10/07 17:37:41] Transfer summary:
  -----------------
  Total files transferred: 2
  Transfer successfully:   2
  Transfer skipped:        0
  Transfer failed:         0
  Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>Resolução de problemas

- Quando utilizar AZCopy, se vir o erro "Ocorreu uma falha ao autenticar o pedido de servidor. Certifique-se que o valor do cabeçalho de autorização está formatado corretamente, incluindo a assinatura." e estiver a utilizar o chave 2 ou a tecla de armazenamento secundário, tente utilizar a chave primária ou 1. º de armazenamento.


## <a name="next-steps"></a>Próximos passos

- Pode criar uma nova VM por [anexar a cópia do VHD para um VM como um disco SO](virtual-machines-windows-create-vm-specialized.md).












