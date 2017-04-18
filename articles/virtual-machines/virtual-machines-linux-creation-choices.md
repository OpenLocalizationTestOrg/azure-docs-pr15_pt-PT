<properties
    pageTitle="Diferentes formas de criar uma VM Linux | Microsoft Azure"
    description="Veja várias formas para criar uma máquina de virtual Linux no Azure, incluindo ligações para ferramentas e tutoriais para cada método."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Diferentes formas de criar uma máquina de virtual Linux no Azure

Tem a flexibilidade no Azure para criar uma máquina de virtual Linux (VM) utilizar as ferramentas e fluxos de trabalho à vontade com a si. Este artigo resume estes diferenças e exemplos para criar o seu VMs Linux.


## <a name="azure-cli"></a>Clip Azure 

O clip do Azure está disponível nas plataformas através de um pacote de npm, pacotes distro fornecido pela ou contentor Docker. Pode ler mais sobre [como instalar e configurar o clip do Azure](../xplat-cli-install.md). Os tutoriais seguintes fornecem exemplos sobre como utilizar o clip do Azure. Leia cada artigo para obter mais detalhes sobre os comandos de guia clip apresentados:

- [Criar uma VM Linux a partir do clip do Azure para Dev Center e teste](virtual-machines-linux-quick-create-cli.md)
    - O exemplo seguinte cria uma VM CoreOS utilizar uma chave pública denominada `azure_id_rsa.pub`:

    ```bash
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
        --image-urn CoreOS
    ```

- [Criar uma VM Linux protegida utilizando um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
    - O exemplo seguinte cria uma VM através de um modelo guardado no GitHub:

    ```bash
    azure group create --name TestRG --location WestUS 
        --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```

- [Criar um ambiente completo do Linux utilizando o clip do Azure](virtual-machines-linux-create-cli-complete.md)
    - Inclui a criação de um balanceador de carga e várias VMs num conjunto de disponibilidade.

- [Adicionar um disco a uma VM Linux](virtual-machines-linux-add-disk.md)
    - O exemplo seguinte adiciona um disco 5Gb um VM existente com o nome `TestVM`:

    ```bash
    azure vm disk attach-new --resource-group TestRG --vm-name TestVM \
        --size-in-GB 5
    ```

## <a name="azure-portal"></a>Portal do Azure

O [Azure portal](https://portal.azure.com) permite-lhe criar rapidamente uma VM uma vez que não há nada instalar no seu sistema. Utilize o portal do Azure para criar a VM:

- [Criar uma VM Linux através do portal Azure](virtual-machines-linux-quick-create-portal.md) 
- [Anexar um disco utilizando o portal do Azure](virtual-machines-linux-attach-disk-portal.md)


## <a name="operating-system-and-image-choices"></a>Sistema operativo e opções de imagem
Ao criar uma VM, tem de escolher uma imagem com base no sistema operativo que pretende executar. Azure e os seus parceiros oferecem várias imagens, algumas das quais incluam aplicações e ferramentas pré-instaladas. Em alternativa, carregar uma das suas próprias imagens (consulte [a secção seguinte](#use-your-own-image)).

### <a name="azure-images"></a>Imagens Azure
Utilizar o `azure vm image` comandos clip para ver o que está disponível ao publisher, distro lançamento e compilações.

Lista fabricantes disponíveis da seguinte forma:

```bash
azure vm image list-publishers --location WestUS
```

Lista produtos disponíveis (ofertas) para um determinado fabricante da seguinte forma:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Liste SKUs disponíveis (distro lançamentos) de uma determinada oferta da seguinte forma:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Liste todas as imagens disponíveis para um determinado lançamento segue:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Para obter mais exemplos sobre como navegar e utilizar imagens disponíveis, consulte o artigo [navegar e selecionar máquina virtual Azure imagens com o clip do Azure](virtual-machines-linux-cli-ps-findimage.md).

O `azure vm quick-create` e `azure vm create` comandos têm aliases que pode utilizar para aceder rapidamente a distros mais comuns e os respetivos versões mais recentes. Utilizar o alias de muitas vezes é mais rápido do que especificando o publisher, oferta, SKU e versão de cada vez que cria uma VM:

| Alias     | Publisher | Oferta        | SKU         | Versão |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | Centos       | 7.2         | mais recente  |
| CoreOS    | CoreOS    | CoreOS       | Estável      | mais recente  |
| Debian    | credativ  | Debian       | 8           | mais recente  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | mais recente  |
| RHEL      | Sistema Redhat    | RHEL         | 7.2         | mais recente  |
| SLES      | SLES      | SLES         | 12-SP1      | mais recente  |
| UbuntuLTS | Canónico | UbuntuServer | 14.04.4-LTS | mais recente  |

### <a name="use-your-own-image"></a>Utilizar a sua própria imagem

Se necessitar de personalizações específicas, pode utilizar uma imagem com base numa VM Azure existente, *capturando* esse VM. Também pode carregar uma imagem criada no local. Para mais informações sobre distros suportados e como utilizar as suas próprias imagens, consulte os artigos seguintes:

- [Azure menção distribuições](virtual-machines-linux-endorsed-distros.md)

- [Informações para não menção distribuições](virtual-machines-linux-create-upload-generic.md)

- [Como capturar uma máquina de virtual Linux como um modelo de Gestor de recursos](virtual-machines-linux-capture-image.md).
    - Guia comandos de exemplo para capturar uma VM existente:

    ```bash
    azure vm deallocate --resource-group TestRG --vm-name TestVM
    azure vm generalize --resource-group TestRG --vm-name TestVM
    azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
    ```

## <a name="next-steps"></a>Próximos passos

- Crie uma VM Linux a partir do [portal](virtual-machines-linux-quick-create-portal.md), com o [clip](virtual-machines-linux-quick-create-cli.md)ou utilizar um [modelo de Gestor de recursos do Azure](virtual-machines-linux-cli-deploy-templates.md).

- Depois de criar um VM Linux, [Adicionar um disco de dados](virtual-machines-linux-add-disk.md).

- Passos rápidos para [Repor uma palavra-passe ou SSH teclas e gerir utilizadores](virtual-machines-linux-using-vmaccess-extension.md)
