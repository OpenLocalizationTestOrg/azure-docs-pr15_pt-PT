<properties
    pageTitle="Preparar Debian Linux VHD | Microsoft Azure"
    description="Saiba como criar Debian 7 e 8 ficheiros VHD para implementação no Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>



# <a name="prepare-a-debian-vhd-for-azure"></a>Preparar um VHD Debian Azure

## <a name="prerequisites"></a>Pré-requisitos
Esta secção assume que já tem instalado um sistema operativo do Debian Linux a partir de um ficheiro. ISO transferido a partir do [Web site Debian](https://www.debian.org/distrib/) para um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd; Hyper-V é apenas um exemplo. Para obter instruções sobre como utilizar o Hyper-V, consulte o artigo [instalar o função Hyper-V e configurar uma Máquina Virtual](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="installation-notes"></a>Notas de instalação

- Consulte o artigo também [Notas de instalação Linux gerais](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obter mais sugestões sobre como preparar Linux para Azure.
- O formato mais recente do VHDX não é suportado no Azure. Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o cmdlet **vhd converter** .
- Ao instalar o sistema Linux é recomendado que utiliza a partições padrão em vez de LVM (muitas vezes a predefinição para muitas instalações). Isto irá evitar LVM nome está em conflito com clonado VMs, sobretudo, se um disco SO nunca tem de ser anexado a outra VM para resolução de problemas. [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) pode ser utilizados em discos dados se preferido.
- Não a configurar uma partição de trocar no disco SO. O agente do Azure Linux pode ser configurado para criar um ficheiro de trocar no disco recurso temporário. Podem encontrar mais informações sobre esta nos passos abaixo.
- Todos os VHDs têm de ter os tamanhos que são múltiplos de 1 MB.


## <a name="use-azure-manage-to-create-debian-vhds"></a>Utilizar o Azure de gerir para criar Debian VHDs

Existem ferramentas disponíveis para gerar VHDs Debian para Azure, tais como a [azure-gerir](https://github.com/credativ/azure-manage) scripts de [credativ](http://www.credativ.com/). Esta é a abordagem recomendada versus criar uma imagem de raiz. Por exemplo, para criar um VHD 8 Debian, execute os seguintes comandos para transferir o azure de gerir (e dependências) e executar o script azure_build_image:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Preparar manualmente um VHD Debian

1. No Gestor de Hyper-V, selecione a máquina virtual.

2. Clique em **Ligar** para abrir uma janela de consola para a máquina virtual.

3. Comentário saída a linha para o **CD-ROM do Debora** no `/etc/apt/source.list` se configurou o VM relativamente a um ficheiro ISO.

4. Editar o `/etc/default/grub` de ficheiros e modificar o parâmetro **GRUB_CMDLINE_LINUX** da seguinte forma para incluir os parâmetros de kernel adicionais do Azure.

        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"

5. Reconstruir a larvas e executar:

        # sudo update-grub

6. Adicione repositórios Azure do Debian a /etc/apt/sources.list para Debian 7 ou 8:

    **7. x debian "Wheezy"**

        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main


    **8 debian "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


7. Instale o Azure agente de Linux:

        # sudo apt-get update
        # sudo apt-get install waagent

8. Debian 7, é necessário para executar o kernel com base em 3.16 a partir do repositório de wheezy backports. Primeiro, crie um ficheiro denominado /etc/apt/preferences.d/linux.pref com o seguinte conteúdo:

        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500

    Em seguida, execute "instalar possam obter sudo linux-imagem-amd64" para instalar o kernel novo.

8. Deprovision máquina virtual e prepará-lo para aprovisionamento no Azure e executar:

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. Clique em **ação** -> Encerrar para baixo no Gestor de Hyper-V. O VHD Linux está agora pronto para ser carregado para Azure.


## <a name="next-steps"></a>Próximos passos

Agora está pronto para utilizar o seu disco rígido virtual Debian para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para Azure, consulte os passos 2 e 3 em [criar e carregar um disco rígido virtual que contém o sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).
