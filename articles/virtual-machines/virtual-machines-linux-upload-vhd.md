<properties
    pageTitle="Criar e carregar uma imagem personalizada do Linux | Microsoft Azure"
    description="Criar e carregar um disco rígido virtual (VHD) para o Azure com uma imagem Linux personalizada utilizando o modelo de implementação do Gestor de recursos."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

# <a name="upload-and-create-a-linux-vm-from-custom-disk-image"></a>Carregar e criar uma VM Linux da imagem do disco personalizada

Este artigo mostra-lhe como carregar um disco rígido virtual (VHD) para utilizar o modelo de implementação do Gestor de recursos do Azure e criar Linux VMs a partir desta imagem personalizada. Esta funcionalidade permite-lhe instalar e configurar uma distro Linux com os seus requisitos e, em seguida, utilize esse VHD para criar rapidamente Azure máquinas virtuais (VMs).

## <a name="quick-commands"></a>Comandos rápidos
Se precisa de realizar rapidamente a tarefa, os seguintes detalhes de secção a base para os comandos para carregar uma VM para Azure. Obter informações mais detalhadas e o contexto para cada passo podem encontrar o resto do documento, [começando aqui](#requirements).

Certifique-se de que tem [O clip do Azure](../xplat-cli-install.md) sessão iniciada e utilizar o modo de Gestor de recursos:

```bash
azure config mode arm
```

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Exemplo os nomes dos parâmetros incluídos `myResourceGroup`, `mystorageaccount`, e `myimages`.

Primeiro, crie um grupo de recursos. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na `WestUs` localização:

```bash
azure group create myResourceGroup --location "WestUS"
```

Crie uma conta de armazenamento para colocar em espera seus discos virtuais. O exemplo seguinte cria uma conta de armazenamento denominada `mystorageaccount`:

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

As teclas de acesso para a sua conta de armazenamento da lista. Tome nota do `key1`:

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Crie um contentor na sua conta de armazenamento com a chave de armazenamento obteve. O exemplo seguinte cria um contentor denominado `myimages` utilizando o valor de chave do armazenamento dos `key1`:

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Por fim, carregue o seu VHD para o contentor que criou. Especificar o caminho local para o seu VHD em `/path/to/disk/mydisk.vhd`:

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

Agora pode criar uma VM a partir do seu disco virtual carregados [através de um modelo de Gestor de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). Também pode utilizar o clip, especificando o URI para o seu disco (`--image-urn`). O exemplo seguinte cria uma VM denominada `myVM` utilizar o disco virtual carregados previamente:

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

A conta de armazenamento de destino tem de ser igual a onde carregou disco virtual para. Também precisa de especificar ou resposta pede-lhe para todos os parâmetros adicionais necessários pelo `azure vm create` comando como rede virtual, o endereço IP público, o nome de utilizador e o SSH teclas. Pode ler mais sobre os [parâmetros de Gestor de recursos do clip disponíveis](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Requisitos de
Para concluir os passos seguintes, é necessário:

- **Sistema operativo Linux instalado num ficheiro. vhd** - instalar uma [distribuição menção Azure Linux](virtual-machines-linux-endorsed-distros.md) (ou ver as [informações de que não sejam menção distribuições](virtual-machines-linux-create-upload-generic.md)) para um disco virtual no formato de VHD. Existem várias ferramentas para criar um VM e VHD:
    - Instalar e configurar [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), tendo o cuidado de utilizar VHD como o formato de imagem. Se for necessário, pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) utilizando `qemu-img convert`.
    - Também pode utilizar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] O formato mais recente do VHDX não é suportado no Azure. Quando cria uma VM, especifique VHD como o formato. Se for necessário, pode converter discos VHDX VHD utilizando [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou o [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) cmdlet do PowerShell. Além disso, Azure não suporta carregar VHDs dinâmicos, pelo que necessita converter esses discos VHDs estáticos antes de carregar. Pode utilizar ferramentas, como o [Azure VHD utilitários para ir](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter discos dinâmicos durante o processo de carregar para Azure.

- VMs criadas a partir de uma imagem personalizada tem residem na mesma conta de armazenamento como a própria imagem
    - Criar uma conta de armazenamento e contentor para manter a sua imagem personalizada e de VMs criados
    - Depois de ter criado todos os seus VMs, pode eliminar a imagem em segurança

Certifique-se de que tem [O clip do Azure](../xplat-cli-install.md) sessão iniciada e utilizar o modo de Gestor de recursos:

```bash
azure config mode arm
```

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Exemplo os nomes dos parâmetros incluídos `myResourceGroup`, `mystorageaccount`, e `myimages`.


<a id="prepimage"> </a>
## <a name="prepare-the-image-to-be-uploaded"></a>Preparar a imagem ser carregado

Azure suporta vários Linux distribuições (consulte [Menção distribuições](virtual-machines-linux-endorsed-distros.md)). Os seguintes artigos orientam-no como preparar o as distribuições Linux vários que são suportadas no Azure:

- **[Com base em centOS distribuições](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Chapéu vermelho Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Outras - distribuições não menção](virtual-machines-linux-create-upload-generic.md)**

Consulte também as **[Notas de instalação Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** para sugestões mais gerais sobre como preparar Linux imagens para Azure.

> [AZURE.NOTE] Se aplica a [plataforma Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) ao VMs executar Linux apenas quando uma das distribuições menção é utilizada com os detalhes de configuração conforme especificado em 'Suportadas versões' na [Linux em Azure-Endorsed distribuições](virtual-machines-linux-endorsed-distros.md).


## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Grupos de recursos logicamente reúnem todos os recursos Azure para suportar as máquinas virtuais, como o funcionamento em rede virtual e armazenamento. Leia mais sobre os [grupos de recurso Azure aqui](../azure-resource-manager/resource-group-overview.md). Antes de carregar a imagem do disco personalizada e criar VMs, primeiro tem de criar um grupo de recursos. 

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na `WestUS` localização:

```bash
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
VMs são armazenados como blobs página dentro de uma conta de armazenamento. Leia mais sobre o [armazenamento de Blobs do Azure aqui](../storage/storage-introduction.md#blob-storage). Criar uma conta de armazenamento para a sua imagem de disco personalizada e VMs. Qualquer VMs que cria a partir da sua imagem do disco personalizada necessitam de ser a mesma conta de armazenamento como imagem.

O exemplo seguinte cria uma conta de armazenamento denominada `mystorageaccount` no grupo de recursos criado anteriormente:

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Lista de teclas de conta de armazenamento
Azure gera duas teclas de acesso de 512 bits para cada conta de armazenamento. Estas teclas de acesso são utilizadas quando autentica para a conta de armazenamento, tais como efectuar operações de escrita. Leia mais sobre como [Gerir o acesso ao armazenamento aqui](../storage/storage-create-storage-account.md#manage-your-storage-account). Pode ver teclas de acesso com a `azure storage account keys list` comando.

Ver as teclas de acesso para a conta de armazenamento que criou:

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

O resultado é semelhante a:

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
Tome nota do `key1` à medida que o irá utilizar para interagir com a sua conta de armazenamento nos próximos passos.

## <a name="create-a-storage-container"></a>Criar um contentor de armazenamento
Da mesma forma que criar directórios diferentes para organizar o seu sistema de ficheiros local logicamente, criar contentores dentro de uma conta de armazenamento para organizar os seus discos virtuais e imagens. Uma conta de armazenamento pode conter qualquer número de membros em contentores. 

O exemplo seguinte cria um contentor denominado `myimages`, especificando a tecla de acesso obtido no passo anterior (`key1`):

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Carregar VHD
Agora pode realmente carregar a imagem do disco personalizada. Como com todos os discos virtuais utilizados pelo VMs, carrega e armazenar a imagem do disco personalizada como um blob de página.

Especifique a sua chave de acesso, o contentor que criou no passo anterior e, em seguida, o caminho para a imagem do disco personalizada no seu computador local:

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Criar VM a partir de imagem personalizada
Quando cria VMs da sua imagem do disco personalizada, especifique o URI para a imagem de disco. Certifique-se de que as correspondências de conta de armazenamento de destino localização onde armazenou a imagem do disco personalizada. Pode criar a VM utilizando o modelo Azure clip ou JSON Gestor de recursos.


### <a name="create-a-vm-using-the-azure-cli"></a>Criar uma VM utilizando o clip do Azure
Especificar o `--image-urn` parâmetro com o `azure vm create` comando para apontar para a sua imagem do disco personalizada. Certifique-se de que `--storage-account-name` corresponde à conta de armazenamento a localização onde armazenou a imagem do disco personalizada. Não possui a utilizar o mesmo contentor como a imagem do disco personalizada para armazenar o seu VMs. Certifique-se criar quaisquer contentores adicionais da mesma forma como os passos anteriores antes de carregar as imagens de disco personalizada.

O exemplo seguinte cria uma VM denominada `myVM` da sua imagem do disco personalizada:

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

Ainda precisa de especificar ou resposta pede-lhe para todos os parâmetros adicionais necessários pelo `azure vm create` comando como rede virtual, o endereço IP público, o nome de utilizador e o SSH teclas. Leia mais sobre os [parâmetros de Gestor de recursos do clip disponíveis](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Criar uma VM através de um modelo JSON
Modelos de Gestor de recursos Azure são ficheiros de JavaScript objeto notação (JSON) que definem o ambiente que pretende criar. Os modelos são divididos para fornecedores de recursos diferentes como cluster ou de rede. Pode utilizar os modelos existentes ou escreva o seu próprio. Leia mais sobre como [utilizar o Gestor de recursos e modelos](../azure-resource-manager/resource-group-overview.md).

Dentro de `Microsoft.Compute/virtualMachines` fornecedor do seu modelo, que tem um `storageProfile` nó que contém os detalhes de configuração para sua VM. São os dois parâmetros principais para editar o `image` e `vhd` URIs que apontem para a sua imagem do disco personalizada e disco virtual do seu VM novo. A imagem seguinte mostra um exemplo de JSON para utilizar uma imagem do disco personalizado:

```bash
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Pode utilizar [Este modelo para criar uma VM a partir de uma imagem personalizada existente](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ou leia sobre como [criar os seus próprios modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md). 

Assim que tiver um modelo configurado, criar seu VMs utilizando o `azure group deployment create` comando. Especifique o URI o modelo de JSON com o `--template-uri` parâmetro:

```bash
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Se tiver um ficheiro JSON armazenado localmente no seu computador, pode utilizar o `--template-file` parâmetro em vez disso:

```bash
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Próximos passos
Depois de ter preparado e carregados disco virtual personalizado, pode ler mais sobre como [utilizar o Gestor de recursos e modelos](../azure-resource-manager/resource-group-overview.md). Também poderá querer para [Adicionar um disco de dados](virtual-machines-linux-add-disk.md) para o seu novos VMs. Se tiver aplicações em execução no seu VMs que precisa de aceder, certifique-se de que [Abrir portas e os pontos finais](virtual-machines-linux-nsg-quickstart.md).