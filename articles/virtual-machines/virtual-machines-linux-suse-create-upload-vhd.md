<properties
    pageTitle="Criar e carregar um VHD de Linux SUSE no Azure"
    description="Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operativo de SUSE Linux."
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

# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Preparar uma máquina de virtual SLES ou openSUSE Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Pré-requisitos ##

Este artigo assume que já tiver instalado um SUSE ou openSUSE sistema operativo Linux para um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd, por exemplo uma solução de virtualização como Hyper-V. Para obter instruções, consulte o artigo [instalar o função Hyper-V e configurar uma Máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES / openSUSE notas de instalação

- Consulte o artigo também [Notas de instalação Linux gerais](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obter mais sugestões sobre como preparar Linux para Azure.

- O formato VHDX não é suportado no Azure, apenas **corrigido VHD**.  Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o cmdlet vhd converter.

- Ao instalar o sistema Linux é recomendado que utiliza a partições padrão em vez de LVM (muitas vezes a predefinição para muitas instalações). Isto irá evitar LVM nome está em conflito com clonado VMs, sobretudo, se um disco SO nunca tem de ser anexado a outra VM para resolução de problemas. [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) pode ser utilizados em discos dados se preferido.

- Não a configurar uma partição de trocar no disco SO. O agente de Linux pode ser configurado para criar um ficheiro de trocar no disco recurso temporário.  Podem encontrar mais informações sobre esta nos passos abaixo.

- Todos os VHDs têm de ter os tamanhos que são múltiplos de 1 MB.


## <a name="use-suse-studio"></a>Utilizar o SUSE Studio
[SUSE Studio](http://www.susestudio.com) pode facilmente criar e gerir as imagens SLES e openSUSE para Azure e Hyper-V. Esta é a abordagem recomendada para personalizar as suas próprias imagens SLES e openSUSE.

Como alternativa para construir a sua própria VHD, SUSE também publica imagens BYOS (trazer o próprio de subscrição) para SLES na [VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007).


## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Preparar SUSE Linux Enterprise Server 11 SP4 ##

1. No painel central do Gestor de Hyper-V de, selecione a máquina virtual.

2. Clique em **Ligar** para abrir a janela para a máquina virtual.

3. Registe-se o seu sistema de SUSE Linux Enterprise para permitir que as atualizações de transferir e instalar pacotes.

4. Atualize o sistema com os patches mais recentes:

        # sudo zypper update

5. Instale o agente de Linux Azure a partir do repositório SLES:

        # sudo zypper install WALinuxAgent

6. Verificar se waagent é definido como "a fazer" no chkconfig e, caso não esteja, ativá-lo para iniciar automaticamente:
               
        # sudo chkconfig waagent on

7. Verifique se waagent serviço está a executar e, caso não esteja, iniciá-lo: 

        # sudo service waagent start
                
8. Modificar a linha de arranque kernel na sua configuração larvas para incluir os parâmetros de kernel adicionais do Azure. Para efetuar este abrir "/ boot/grub/menu.lst" num editor de texto e certifique-se de que o kernel predefinido inclui os seguintes parâmetros:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Isto irá garantir a todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração.

9. Confirme que /boot/grub/menu.lst /etc/fstab ambos de referência e o disco utilizando o respetivo UUID (por uuid) em vez do ID do disco (por id). 

    Obtenha disco UUID
    
        # ls /dev/disk/by-uuid/

    Se /dev/disk/by-id / é utilizado, atualizar fstab /boot/grub/menu.lst tanto/etc/com o valor inicial por uuid

    Antes da alteração
    
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1

    Depois de alteração
    
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

10. Modificar udev regras para evitar gerar estáticas regras para a interface de Ethernet (s). Estas regras podem causar problemas quando clonar uma máquina virtual no Microsoft Azure ou Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

11. É recomendado para editar o ficheiro "/ etc/sysconfig/rede/dhcp" e alterar o `DHCLIENT_SET_HOSTNAME` parâmetro para o seguinte:

        DHCLIENT_SET_HOSTNAME="no"

12. Na "/ etc/sudoers", comentar ou remova as linhas seguintes caso existam:

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

13. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado durante o arranque.  Normalmente, esta é a predefinição.

14. Não crie trocar espaço no disco SO.

    O agente de Linux Azure pode configurar automaticamente o espaço de trocar utilizando o disco de recursos locais é anexado à VM depois de aprovisionamento no Azure. Repare que o disco de recursos locais é um disco *temporário* e pode ser esvaziado quando a VM será descontinuada. Depois de instalar o agente de Linux Azure (consulte o passo anterior), modificar seguintes parâmetros na /etc/waagent.conf corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15. Execute os seguintes comandos deprovision máquina virtual e prepará-lo para aprovisionar no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. Clique em **ação -> Encerrar para baixo** no Gestor de Hyper-V. O VHD Linux está agora pronto para ser carregado para Azure.


----------

## <a name="prepare-opensuse-131"></a>Preparar openSUSE 13.1 + ##

1. No painel central do Gestor de Hyper-V de, selecione a máquina virtual.

2. Clique em **Ligar** para abrir a janela para a máquina virtual.

3. No shell, execute o comando '`zypper lr`'. Se este comando devolve o resultado semelhante ao seguinte, em seguida, repositórios estão configurados como esperado – forem necessários sem ajustes (tenha em atenção que os números de versão podem variar):

        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

    Se o comando devolver "Sem repositórios definidos...", em seguida, utilize os seguintes comandos para adicionar estes repouso:

        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

    Em seguida, pode verificar repositórios foram adicionados ao executar o comando '`zypper lr`' novamente. No caso de uma das repositórios actualização relevante não estiver ativada, ativá-lo com o seguinte comando:

        # sudo zypper mr -e [NUMBER OF REPOSITORY]


4. Atualize o kernel para a versão mais recente:

        # sudo zypper up kernel-default

    Ou para atualizar o sistema com todos os patches mais recentes:

        # sudo zypper update

5.  Instale o Azure agente de Linux.

        # sudo zypper install WALinuxAgent

6.  Modificar a linha de arranque kernel na sua configuração larvas para incluir os parâmetros de kernel adicionais do Azure. Para executar esta tarefa, abra "/ boot/grub/menu.lst" num editor de texto e certifique-se de que o kernel predefinido inclui os seguintes parâmetros:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Isto irá garantir a todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Além disso, remova seguintes parâmetros da linha de arranque kernel caso existam:

        libata.atapi_enabled=0 reserve=0x1f0,0x8

7.  É recomendado para editar o ficheiro "/ etc/sysconfig/rede/dhcp" e alterar o `DHCLIENT_SET_HOSTNAME` parâmetro para o seguinte:

        DHCLIENT_SET_HOSTNAME="no"

8.  **Importantes:** Na "/ etc/sudoers", comentar ou remova as linhas seguintes caso existam:

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.  Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado durante o arranque.  Normalmente, esta é a predefinição.

10. Não crie trocar espaço no disco SO.

    O agente de Linux Azure pode configurar automaticamente o espaço de trocar utilizando o disco de recursos locais é anexado à VM depois de aprovisionamento no Azure. Repare que o disco de recursos locais é um disco *temporário* e pode ser esvaziado quando a VM será descontinuada. Depois de instalar o agente de Linux Azure (consulte o passo anterior), modificar seguintes parâmetros na /etc/waagent.conf corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Execute os seguintes comandos deprovision máquina virtual e prepará-lo para aprovisionar no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

12. Certifique-se de que o agente de Linux Azure é executado no arranque:

        # sudo systemctl enable waagent.service

13. Clique em **ação -> Encerrar para baixo** no Gestor de Hyper-V. O VHD Linux está agora pronto para ser carregado para Azure.

## <a name="next-steps"></a>Próximos passos
Agora está pronto para utilizar o seu disco rígido virtual SUSE Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para Azure, consulte os passos 2 e 3 em [criar e carregar um disco rígido virtual que contém o sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).
