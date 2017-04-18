<properties
    pageTitle="Criar e carregar um VHD Linux | Microsoft Azure"
    description="Criar e carregar um Azure virtual no disco rígido (VHD) com o modelo clássico de implementação que contém o sistema operativo Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Criar e carregar um disco rígido Virtual que contém o sistema operativo Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Também pode [carregar uma imagem do disco personalizada utilizando o Gestor de recursos do Azure](virtual-machines-linux-upload-vhd.md).

Este artigo mostra-lhe como criar e carregar um disco rígido virtual (VHD) para que possa utilizar como a sua própria imagem para criar máquinas virtuais no Azure. Saiba como preparar o sistema operativo para utilizá-lo para criar várias máquinas virtuais com base na imagem. 

>  [AZURE.NOTE] Se tiver alguns minutos, contacte ajude-na melhorar a documentação do Azure Linux VM ao efetuar este [inquérito rápido](https://aka.ms/linuxdocsurvey) das suas experiências. Cada resposta ajuda-nos ajuda realizar o seu trabalho.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem os seguintes itens:

- **Sistema operativo de Linux instalado num ficheiro. vhd** - instalou uma [distribuição menção Azure Linux](virtual-machines-linux-endorsed-distros.md) (ou ver as [informações de que não sejam menção distribuições](virtual-machines-linux-create-upload-generic.md)) para um disco virtual no formato de VHD. Existem várias ferramentas para criar um VM e VHD:
    - Instalar e configurar [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), tendo o cuidado de utilizar VHD como o formato de imagem. Se for necessário, pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) utilizando `qemu-img convert`.
    - Também pode utilizar Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] O formato mais recente do VHDX não é suportado no Azure. Quando cria uma VM, especifique VHD como o formato. Se for necessário, pode converter discos VHDX VHD utilizando [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou o [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) cmdlet do PowerShell. Além disso, Azure não suporta carregar VHDs dinâmicos, pelo que necessita converter esses discos VHDs estáticos antes de carregar. Pode utilizar ferramentas, como o [Azure VHD utilitários para ir](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter discos dinâmicos durante o processo de carregar para Azure.

- **Interface de comandos do azure** - instalar a mais recente do [Azure Interface de comandos](../virtual-machines-command-line-tools.md) para carregar o VHD.

<a id="prepimage"> </a>
## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Passo 1: Preparar a imagem ser carregado

Azure suporta vários Linux distribuições (consulte [Menção distribuições](virtual-machines-linux-endorsed-distros.md)). Os seguintes artigos orientam-como preparar o as distribuições Linux vários que são suportadas no Azure. Depois de concluir os passos seguintes guias, vir aqui novamente assim que tiver um ficheiro VHD que está pronto para carregar para o Azure:

- **[Com base em centOS distribuições](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Chapéu vermelho Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Outras - distribuições não menção](virtual-machines-linux-create-upload-generic.md)**

> [AZURE.NOTE] A plataforma Azure SLA se aplica ao máquinas virtuais a executar o sistema operativo Linux apenas quando uma das distribuições menção é utilizada com os detalhes de configuração conforme especificado em 'Suportadas versões' na [Linux em Azure-Endorsed distribuições](virtual-machines-linux-endorsed-distros.md). Todas as distribuições Linux na Galeria de imagem Azure são as distribuições menção com a configuração necessária.

Consulte também as **[Notas de instalação Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** para sugestões mais gerais sobre como preparar Linux imagens para Azure.


<a id="connect"> </a>
## <a name="step-2-prepare-the-connection-to-azure"></a>Passo 2: Preparar a ligação do Azure

Certifique-se estiver a utilizar o clip do Azure no modelo de implementação clássico (`azure config mode asm`), em seguida, inicie sessão na sua conta:

```
azure login
```


<a id="upload"> </a>
## <a name="step-3-upload-the-image-to-azure"></a>Passo 3: Carregar a imagem para Azure

É necessária uma conta de armazenamento para carregar o ficheiro VHD para. Ou pode selecionar uma conta de armazenamento existente ou [crie um novo](../storage/storage-create-storage-account.md).

Utilize o clip do Azure para carregar a imagem utilizando o seguinte comando:

```bash
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

No exemplo anterior:

- **BlobStorageURL** é o URL para a conta de armazenamento que pretenda utilizar
- **YourImagesFolder** é o contentor dentro de armazenamento de BLOBs onde pretende armazenar as imagens
- **VHDName** é a etiqueta que aparece no portal para identificar o disco rígido virtual.
- **PathToVHDFile** é o nome do ficheiro. vhd no seu computador e o caminho completo.

A imagem seguinte mostra um exemplo concluído:

```bash
azure vm image create UbuntuLTS `
    --blob-url https://teststorage.blob.core.windows.net/vhds/UbuntuLTS.vhd `
    --os Linux /home/ahmet/UbuntuLTS.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Passo 4: Criar uma VM a partir da imagem
Criar um VM utilizando `azure vm create` da mesma forma como uma VM normal. Especifique o nome que atribuiu a sua imagem no passo anterior. No seguinte exemplo, vamos utilizar o nome de imagem **UbuntuLTS** atribuído no passo anterior:

```bash
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "DeployedUbuntu" UbuntuLTS
```

Para criar o seus próprio VMs, forneça o seu próprio nome de utilizador + palavra-passe, localização, nome de DNS e nome da imagem.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, consulte o artigo [referência Azure clip do modelo de implementação clássica Azure](../virtual-machines-command-line-tools.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload
