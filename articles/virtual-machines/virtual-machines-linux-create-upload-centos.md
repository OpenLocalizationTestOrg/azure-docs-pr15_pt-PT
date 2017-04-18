<properties
    pageTitle="Criar e carregar um VHD Linux CentOS baseadas no Azure"
    description="Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operativo com base em CentOS Linux."
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
    ms.date="05/09/2016"
    ms.author="szarkos"/>

# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Preparar uma máquina de virtual com base em CentOS Azure

- [Preparar uma máquina de virtual CentOS 6. x para Azure](#centos-6x)
- [Preparar uma máquina de virtual CentOS 7.0 + Azure](#centos-70)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Pré-requisitos ##

Este artigo assume que já tem instalado um CentOS (ou derivado semelhante) do sistema operativo Linux um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd, por exemplo uma solução de virtualização como Hyper-V. Para obter instruções, consulte o artigo [instalar o função Hyper-V e configurar uma Máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).


**Notas de instalação centOS**

- Consulte o artigo também [Notas de instalação Linux gerais](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obter mais sugestões sobre como preparar Linux para Azure.

- O formato VHDX não é suportado no Azure, apenas **corrigido VHD**.  Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o cmdlet vhd converter.

- Ao instalar o sistema Linux é recomendado que utiliza a partições padrão em vez de LVM (muitas vezes a predefinição para muitas instalações). Isto irá evitar LVM nome está em conflito com clonado VMs, sobretudo, se um disco SO nunca tem de ser anexado a outra VM para resolução de problemas.  [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) pode ser utilizados em discos dados se preferido.

- AN não é suportada para de grande tamanho VM devido a um erro no versões de kernel Linux abaixo 2.6.37. Este problema principalmente impactos distribuições utilizando o montante kernel chapéu vermelho 2.6.32. Instalação manual do agente Azure Linux (waagent) desactivar automaticamente an na configuração LARVAS para o kernel Linux. Podem encontrar mais informações sobre esta nos passos abaixo.

- Não a configurar uma partição de trocar no disco SO. O agente de Linux pode ser configurado para criar um ficheiro de trocar no disco recurso temporário.  Podem encontrar mais informações sobre esta nos passos abaixo.

- Todos os VHDs têm de ter os tamanhos que são múltiplos de 1 MB.


## <a name="centos-6x"></a>CentOS 6. x ##

1. No Gestor de Hyper-V, selecione a máquina virtual.

2. Clique em **Ligar** para abrir uma janela de consola para a máquina virtual.

3. Desinstale NetworkManager executando o seguinte comando:

        # sudo rpm -e --nodeps NetworkManager

    **Nota:** Se o pacote já não está instalado, este comando irá falhar com uma mensagem de erro. Isto é esperado.

4.  Criar um ficheiro de **rede** com o nome a `/etc/sysconfig/` directory que contém o seguinte texto:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Criar um ficheiro com o nome **ifcfg eth0** na `/etc/sysconfig/network-scripts/` directory que contém o seguinte texto:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Modificar udev regras para evitar gerar estáticas regras para a interface de Ethernet (s). Estas regras podem causar problemas quando clonar uma máquina virtual no Microsoft Azure ou Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules


7. Certifique-se de que o serviço de rede será iniciado durante o arranque, executando o seguinte comando:

        # sudo chkconfig network on


8. **CentOS 6.3 apenas**: Instale os controladores para o Linux Integration Services (lista de vertical).

    **Importante: O passo é apenas válidos para CentOS 6.3 e versões anteriores.**  No CentOS 6.4 + os serviços de integração de Linux são *já disponíveis no kernel do padrão*.

    - Siga as instruções de instalação na [página de transferência de lista vertical](https://www.microsoft.com/en-us/download/details.aspx?id=46842) e instale o rotações/minuto para a sua imagem.  


9. Instale o pacote de python pyasn1 executando o seguinte comando:

        # sudo yum install python-pyasn1

10. Se gostaria de utilizar os mirrors OpenLogic alojadas dentro os Azure centros de dados, em seguida, substitua o ficheiro /etc/yum.repos.d/CentOS-Base.repo seguintes repositórios.  Isto também irá adicionar o repositório de **[openlogic]** inclui pacotes do agente de Azure Linux:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    **Nota:** Os restantes neste guia irão partem do princípio de que está a utilizar, pelo menos, o repo [openlogic], que será utilizada para instalar o agente de Azure Linux abaixo.


11. Adicione a linha seguinte para /etc/yum.conf:

        http_caching=packages

    E **em CentOS 6.3 apenas** adicione a linha seguinte:

        exclude=kernel*

12. Desativar o módulo yum "fastestmirror" ao editar o ficheiro "/ etc/yum/pluginconf.d/fastestmirror.conf" e, em `[main]`, escreva o seguinte:

        set enabled=0

13. Execute o seguinte comando para limpar os metadados yum atual:

        # yum clean all

14. **No CentOS 6.3 apenas**, atualize o kernel utilizando o seguinte comando:

        # sudo yum --disableexcludes=all install kernel

15. Modificar a linha de arranque kernel na sua configuração larvas para incluir os parâmetros de kernel adicionais do Azure. Para executar esta tarefa, abra "/ boot/grub/menu.lst" num editor de texto e certifique-se de que o kernel predefinido inclui os seguintes parâmetros:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Isto também irá assegurar a todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Isto irá desactivar an devido a um erro na versão kernel utilizado pelo CentOS 6.

    Para além de acima, é aconselhável para *Remover* os seguintes parâmetros:

        rhgb quiet crashkernel=auto

    Arranque gráfica e sossegada, não são úteis num ambiente nuvem onde queremos todos os registos sejam enviadas para a porta série.

    O `crashkernel` opção pode ser esquerda configurado se pretender, mas a nota que este parâmetro irá reduzir a quantidade de memória disponível na VM por 128MB ou superior, que podem ser problemático nos tamanhos VM mais pequenos.


16. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado durante o arranque.  Normalmente, esta é a predefinição.

17. Instale o agente de Linux Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent

    Note que instalar o pacote de WALinuxAgent removerá o NetworkManager e pacotes NetworkManager Gnomo caso já não foram removidas, tal como descrito no passo 2.

18. Não crie trocar espaço no disco SO.

    O agente de Linux Azure pode configurar automaticamente o espaço de trocar utilizando o disco de recursos locais é anexado à VM depois de aprovisionamento no Azure. Repare que o disco de recursos locais é um disco *temporário* e pode ser esvaziado quando a VM será descontinuada. Depois de instalar o agente de Linux Azure (consulte o passo anterior), modificar seguintes parâmetros na /etc/waagent.conf corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

19. Execute os seguintes comandos deprovision máquina virtual e prepará-lo para aprovisionar no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

20. Clique em **ação -> Encerrar para baixo** no Gestor de Hyper-V. O VHD Linux está agora pronto para ser carregado para Azure.


----------


## <a name="centos-70"></a>CentOS 7.0 + ##

**Alterações no CentOS 7 (e derivados semelhantes)**

Preparar uma máquina de virtual CentOS 7 Azure é muito semelhante a 6 de CentOS, no entanto, existem várias diferenças importantes salientar:

 - O pacote de NetworkManager já não está em conflito com o agente de Azure Linux. Este pacote é instalado por predefinição e recomendamos que não é removido.
 - GRUB2 agora são utilizados como bootloader predefinido, para que o procedimento para edição parâmetros kernel foi alterado (ver abaixo).
 - XFS é agora o sistema de ficheiro predefinido. Ainda pode ser utilizado o sistema de ficheiros ext4 se pretender.


**Passos de configuração**

1. No Gestor de Hyper-V, selecione a máquina virtual.

2. Clique em **Ligar** para abrir uma janela de consola para a máquina virtual.

3.  Criar um ficheiro de **rede** com o nome a `/etc/sysconfig/` directory que contém o seguinte texto:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  Criar um ficheiro com o nome **ifcfg eth0** na `/etc/sysconfig/network-scripts/` directory que contém o seguinte texto:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Modificar udev regras para evitar gerar estáticas regras para a interface de Ethernet (s). Estas regras podem causar problemas quando clonar uma máquina virtual no Microsoft Azure ou Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Certifique-se de que o serviço de rede será iniciado durante o arranque, executando o seguinte comando:

        # sudo chkconfig network on

7. Instale o pacote de python pyasn1 executando o seguinte comando:

        # sudo yum install python-pyasn1

8. Se gostaria de utilizar os mirrors OpenLogic alojadas dentro os Azure centros de dados, em seguida, substitua o ficheiro /etc/yum.repos.d/CentOS-Base.repo seguintes repositórios.  Isto também irá adicionar o repositório de **[openlogic]** inclui pacotes do agente de Azure Linux:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7



    **Nota:** Os restantes neste guia irão partem do princípio de que está a utilizar, pelo menos, o repo [openlogic], que será utilizada para instalar o agente de Azure Linux abaixo.

9.  Execute o seguinte comando para limpar os metadados yum atual e instale as atualizações:

        # sudo yum clean all
        # sudo yum -y update

10. Modificar a linha de arranque kernel na sua configuração larvas para incluir os parâmetros de kernel adicionais do Azure. Para efetuar esta ação, abrir "/ predefinido/etc/larvas" num editor de texto e editar o `GRUB_CMDLINE_LINUX` parâmetro, por exemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

    Isto também irá assegurar a todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Também desativa as as novas convenções de nomenclatura CentOS 7 para NIC. Para além de acima, é aconselhável para *Remover* os seguintes parâmetros:

        rhgb quiet crashkernel=auto

    Arranque gráfica e sossegada, não são úteis num ambiente nuvem onde queremos todos os registos sejam enviadas para a porta série.

    O `crashkernel` opção pode ser esquerda configurado se pretender, mas a nota que este parâmetro irá reduzir a quantidade de memória disponível na VM pelo 128MB ou superior, que podem ser problemático nos tamanhos VM mais pequenos.

11. Depois de ter edição "/ predefinido/etc/larvas" por acima, execute o seguinte comando para recriar a configuração de larvas:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado durante o arranque.  Normalmente, esta é a predefinição.

13. **Apenas se construir a imagem a partir do VMWare, VirtualBox ou KVM:** Adicione módulos Hyper-V para initramfs:

    Editar `/etc/dracut.conf`, adicionar conteúdo:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Reconstrua a initramfs:

        # dracut –f -v

14. Instale o agente de Linux Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent

15. Não crie trocar espaço no disco SO.

    O agente de Linux Azure pode configurar automaticamente o espaço de trocar utilizando o disco de recursos locais é anexado à VM depois de aprovisionamento no Azure. Repare que o disco de recursos locais é um disco *temporário* e pode ser esvaziado quando a VM será descontinuada. Depois de instalar o agente de Linux Azure (consulte o passo anterior), modificar seguintes parâmetros na /etc/waagent.conf corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Execute os seguintes comandos deprovision máquina virtual e prepará-lo para aprovisionar no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Clique em **ação -> Encerrar para baixo** no Gestor de Hyper-V. O VHD Linux está agora pronto para ser carregado para Azure.

## <a name="next-steps"></a>Próximos passos
Agora está pronto para utilizar o seu disco rígido virtual CentOS Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para Azure, consulte os passos 2 e 3 em [criar e carregar um disco rígido virtual que contém o sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).
