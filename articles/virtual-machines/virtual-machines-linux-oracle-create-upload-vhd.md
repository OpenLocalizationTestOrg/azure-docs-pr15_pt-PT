<properties
    pageTitle="Criar e carregar um VHD de Linux Oracle | Microsoft Azure"
    description="Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operativo do Oracle Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Preparar uma máquina de virtual Oracle Linux para Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Pré-requisitos ##

Este artigo assume que já tem instalado num sistema operativo do Oracle Linux para um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd, por exemplo uma solução de virtualização como Hyper-V. Para obter instruções, consulte o artigo [instalar o função Hyper-V e configurar uma Máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).


### <a name="oracle-linux-installation-notes"></a>Notas de instalação do Linux Oracle

- Consulte o artigo também [Notas de instalação Linux gerais](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obter mais sugestões sobre como preparar Linux para Azure.

- Kernel compatível do Oracle chapéu vermelho e os respetivos UEK3 (inviolável Enterprise Kernel) são ambos suportados no Hyper-V e Azure. Para obter melhores resultados, certifique-se de que atualizar para o mais recente kernel enquanto estiver a preparar o seu VHD de Linux Oracle.

- UEK2 do Oracle não é suportada no Hyper-V e Azure como não inclui os controladores necessários.

- O formato VHDX não é suportado no Azure, apenas **fixa VHD**.  Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o cmdlet vhd converter.

- Ao instalar o sistema Linux é recomendado que utiliza a partições padrão em vez de LVM (muitas vezes a predefinição para muitas instalações). Isto irá evitar LVM nome está em conflito com clonado VMs, sobretudo, se um disco SO nunca tem de ser anexado a outra VM para resolução de problemas. [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) pode ser utilizados em discos dados se preferido.

- AN não é suportada para de grande tamanho VM devido a um erro no versões de kernel Linux abaixo 2.6.37. Este problema principalmente impactos distribuições utilizando o montante kernel chapéu vermelho 2.6.32. Instalação manual do agente Azure Linux (waagent) desactivar automaticamente an na configuração LARVAS para o kernel Linux. Podem encontrar mais informações sobre esta nos passos abaixo.

- Não a configurar uma partição de trocar no disco SO. O agente de Linux pode ser configurado para criar um ficheiro de trocar no disco recurso temporário.  Podem encontrar mais informações sobre esta nos passos abaixo.

- Todos os VHDs têm de ter os tamanhos que são múltiplos de 1 MB.

- Certifique-se de que o `Addons` repositório está ativado. Editar o ficheiro `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux) e altere a linha `enabled=0` para `enabled=1` em **[ol6_addons]** ou **[ol7_addons]** neste ficheiro.


## <a name="oracle-linux-64"></a>Oracle Linux 6.4 + ##

Tem de concluir os passos de configuração específicas no sistema operativo para a máquina virtual executar no Azure.

1. No painel central do Gestor de Hyper-V de, selecione a máquina virtual.

2. Clique em **Ligar** para abrir a janela para a máquina virtual.

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

        # chkconfig network on

8. Instale python pyasn1 executando o seguinte comando:

        # sudo yum install python-pyasn1

9.  Modificar a linha de arranque kernel na sua configuração larvas para incluir os parâmetros de kernel adicionais do Azure. Para efetuar este abrir "/ boot/grub/menu.lst" num editor de texto e certifique-se de que o kernel predefinido inclui os seguintes parâmetros:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Isto também irá assegurar a todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Isto irá desactivar an devido a um erro no kernel compatível de chapéu vermelho do Oracle.

    Para além de acima, é aconselhável para *Remover* os seguintes parâmetros:

        rhgb quiet crashkernel=auto

    Arranque gráfica e sossegada, não são úteis num ambiente nuvem onde queremos todos os registos sejam enviadas para a porta série.

    O `crashkernel` opção pode ser esquerda configurado se pretender, mas a nota que este parâmetro irá reduzir a quantidade de memória disponível na VM por 128MB ou superior, que podem ser problemático nos tamanhos VM mais pequenos.


10. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado durante o arranque.  Normalmente, esta é a predefinição.

11. Instale o agente de Linux Azure executando o seguinte comando. A versão mais recente está 2.0.15.

        # sudo yum install WALinuxAgent

    Note que instalar o pacote de WALinuxAgent removerá o NetworkManager e pacotes NetworkManager Gnomo caso já não foram removidas, tal como descrito no passo 2.

12. Não crie trocar espaço no disco SO.

    O agente de Linux Azure pode configurar automaticamente o espaço de trocar utilizando o disco de recursos locais é anexado à VM depois de aprovisionamento no Azure. Repare que o disco de recursos locais é um disco *temporário* e pode ser esvaziado quando a VM será descontinuada. Depois de instalar o agente de Linux Azure (consulte o passo anterior), modificar seguintes parâmetros na /etc/waagent.conf corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Execute os seguintes comandos deprovision máquina virtual e prepará-lo para aprovisionar no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Clique em **ação -> Encerrar para baixo** no Gestor de Hyper-V. O VHD Linux está agora pronto para ser carregado para Azure.


----------


## <a name="oracle-linux-70"></a>Oracle Linux 7.0 + ##

**Alterações no Oracle Linux 7**

Preparar uma máquina de virtual Oracle Linux 7 Azure é muito semelhante a Oracle Linux 6, no entanto, existem várias diferenças importantes salientar:

 - O kernel compatível com chapéu vermelho e UEK3 do Oracle são suportadas no Azure.  É recomendado UEK3 kernel.
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

5.  Modificar udev regras para evitar gerar estáticas regras para a interface de Ethernet (s). Estas regras podem causar problemas quando clonar uma máquina virtual no Microsoft Azure ou Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Certifique-se de que o serviço de rede será iniciado durante o arranque, executando o seguinte comando:

        # sudo chkconfig network on

7. Instale o pacote de python pyasn1 executando o seguinte comando:

        # sudo yum install python-pyasn1

8.  Execute o seguinte comando para limpar os metadados yum atual e instale as atualizações:

        # sudo yum clean all
        # sudo yum -y update

9.  Modificar a linha de arranque kernel na sua configuração larvas para incluir os parâmetros de kernel adicionais do Azure. Para fazer isto abrir "/ predefinido/etc/larvas" num editor de texto e editar o `GRUB_CMDLINE_LINUX` parâmetro, por exemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

    Isto também irá assegurar a todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Também desativa as as novas convenções de nomenclatura OEL 7 para NIC. Para além de acima, é aconselhável para *Remover* os seguintes parâmetros:

        rhgb quiet crashkernel=auto

    Arranque gráfica e sossegada, não são úteis num ambiente nuvem onde queremos todos os registos sejam enviadas para a porta série.

    O `crashkernel` opção pode ser esquerda configurado se pretender, mas a nota que este parâmetro irá reduzir a quantidade de memória disponível na VM por 128MB ou superior, que podem ser problemático nos tamanhos VM mais pequenos.


10. Depois de ter edição "/ predefinido/etc/larvas" por acima, execute o seguinte comando para recriar a configuração de larvas:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

11. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado durante o arranque.  Normalmente, esta é a predefinição.

12. Instale o agente de Linux Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent

13. Não crie trocar espaço no disco SO.

    O agente de Linux Azure pode configurar automaticamente o espaço de trocar utilizando o disco de recursos locais é anexado à VM depois de aprovisionamento no Azure. Repare que o disco de recursos locais é um disco *temporário* e pode ser esvaziado quando a VM será descontinuada. Depois de instalar o agente de Linux Azure (consulte o passo anterior), modificar seguintes parâmetros na /etc/waagent.conf corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Execute os seguintes comandos deprovision máquina virtual e prepará-lo para aprovisionar no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Clique em **ação -> Encerrar para baixo** no Gestor de Hyper-V. O VHD Linux está agora pronto para ser carregado para Azure.


## <a name="next-steps"></a>Próximos passos
Agora está pronto para utilizar o seu. vhd Oracle Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para Azure, consulte os passos 2 e 3 em [criar e carregar um disco rígido virtual que contém o sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).
