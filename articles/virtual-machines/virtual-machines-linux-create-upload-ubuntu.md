<properties
    pageTitle="Criar e carregar um VHD de Linux Ubuntu no Azure"
    description="Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operativo do Ubuntu Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Preparar uma máquina de virtual Ubuntu Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Imagens de nuvem Ubuntu oficiais
Ubuntu agora publica oficial Azure VHDs para transferência nas [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/). Se precisar de criar a sua própria imagem Ubuntu especializada para Azure, em vez de utilizar o procedimento manual abaixo é recomendável para iniciar com estes conhecidos trabalhar VHDs e personalizá-lo conforme necessário. As versões mais recentes da imagem sempre podem ser encontradas em seguintes localizações:

 - Ubuntu 12.04/precisos: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/precise/release/ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 14.04/fiável: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)


## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já tem instalado num sistema operativo do Ubuntu Linux para um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd, por exemplo uma solução de virtualização como Hyper-V. Para obter instruções, consulte o artigo [instalar o função Hyper-V e configurar uma Máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação Ubuntu**

- Consulte o artigo também [Notas de instalação Linux gerais](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obter mais sugestões sobre como preparar Linux para Azure.
- O formato VHDX não é suportado no Azure, apenas **corrigido VHD**.  Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o cmdlet vhd converter.
- Ao instalar o sistema Linux é recomendado que utiliza a partições padrão em vez de LVM (muitas vezes a predefinição para muitas instalações). Isto irá evitar LVM nome está em conflito com clonado VMs, sobretudo, se um disco SO nunca tem de ser anexado a outra VM para resolução de problemas. [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) pode ser utilizados em discos dados se preferido.
- Não a configurar uma partição de trocar no disco SO. O agente de Linux pode ser configurado para criar um ficheiro de trocar no disco recurso temporário.  Podem encontrar mais informações sobre esta nos passos abaixo.
- Todos os VHDs têm de ter os tamanhos que são múltiplos de 1 MB.


## <a name="manual-steps"></a>Passos manuais

> [AZURE.NOTE] Antes de criar a sua própria imagem Ubuntu personalizada para Azure, considere utilizar as imagens a partir do [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/) em vez disso.


1. No painel central do Gestor de Hyper-V de, selecione a máquina virtual.

2. Clique em **Ligar** para abrir a janela para a máquina virtual.

3.  Substitua os repositórios atuais da imagem para utilizar repouso Azure do Ubuntu. Os passos ligeiramente variam consoante a versão de Ubuntu.

    Antes de editar /etc/apt/sources.list, é recomendável para fazer uma cópia de segurança:

        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04:

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

4. As imagens Ubuntu Azure estão agora a seguir o kernel *activação de hardware* (HWE). Atualize o sistema operativo para o mais recente kernel executando os seguintes comandos:

    Ubuntu 12.04:

        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    Ubuntu 14.04:

        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot


5. Modificar a linha de arranque kernel para larvas incluir os parâmetros de kernel adicionais do Azure. Para fazer isto abrir "/ predefinido/etc/larvas" num editor de texto, localize a variável de denominada `GRUB_CMDLINE_LINUX_DEFAULT` (ou adicioná-lo caso seja necessário) e editá-lo para incluir os seguintes parâmetros:

        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Guardar e fechar este ficheiro e, em seguida, execute '`sudo update-grub`'. Isto irá garantir que todas as mensagens de consola são enviadas para a porta série primeira, pode dar assistência técnica Azure com problemas de depuração.

6.  Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado durante o arranque.  Normalmente, esta é a predefinição.

7.  Instale o Azure agente de Linux:

        # sudo apt-get update
        # sudo apt-get install walinuxagent

    Tenha em atenção que instalar o `walinuxagent` pacote removerá o `NetworkManager` e `NetworkManager-gnome` pacotes, se estiverem instalados.

8.  Execute os seguintes comandos deprovision máquina virtual e prepará-lo para aprovisionar no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Clique em **ação -> Encerrar para baixo** no Gestor de Hyper-V. O VHD Linux está agora pronto para ser carregado para Azure.


## <a name="next-steps"></a>Próximos passos
Agora está pronto para utilizar o seu disco rígido virtual Ubuntu Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para Azure, consulte os passos 2 e 3 em [criar e carregar um disco rígido virtual que contém o sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).

## <a name="references"></a>Referências ##

Kernel do Ubuntu hardware activação (HWE):

- [http://blog.utlemming.org/2015/01/ubuntu-1404-Azure-Images-Now-Tracking.HTML](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
- [http://blog.utlemming.org/2015/02/1204-Azure-cloud-Images-Now-using-Hwe.HTML](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)
