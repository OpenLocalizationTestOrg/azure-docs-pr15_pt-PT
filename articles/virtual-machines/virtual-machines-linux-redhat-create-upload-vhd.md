<properties
    pageTitle="Criar e carregar um vermelho chapéu Enterprise Linux VHD para utilização no Azure"
    description="Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operativo do vermelho chapéu Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/17/2016"
    ms.author="mingzhan"/>


# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Preparar uma máquina de virtual com base em vermelho chapéu Azure

Este artigo vai aprender preparar uma máquina de virtual vermelho chapéu Enterprise Linux (RHEL) para utilização no Azure. Versões do RHEL que estejam tapadas neste artigo são 6,7, 7.1 e 7.2. Hypervisors para preparação que estejam tapados neste artigo são Hyper-V, com base em Kernel Máquina Virtual (KVM) e VMware. Para mais informações sobre os requisitos de elegibilidade para participar no programa de acesso à nuvem do vermelho chapéu, consulte o artigo [Web site de acesso à nuvem do vermelho chapéu](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) e [RHEL em execução no Azure](https://access.redhat.com/articles/1989673).

[Preparar uma máquina de virtual RHEL 6,7 do Gestor de Hyper-V](#rhel67hyperv)

[Preparar uma máquina de virtual RHEL 7.1/7.2 do Gestor de Hyper-V](#rhel7xhyperv)

[Preparar uma máquina de virtual RHEL 6,7 a partir de KVM](#rhel67kvm)

[Preparar uma máquina de virtual RHEL 7.1/7.2 a partir de KVM](#rhel7xkvm)

[Preparar uma máquina de virtual RHEL 6,7 a partir de VMware](#rhel67vmware)

[Preparar uma máquina de virtual RHEL 7.1/7.2 a partir de VMware](#rhel7xvmware)

[Preparar uma máquina de virtual RHEL 7.1/7.2 a partir de um ficheiro de kickstart](#rhel7xkickstart)


## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Preparar uma máquina de virtual com base em vermelho chapéu do Gestor de Hyper-V
### <a name="prerequisites"></a>Pré-requisitos
Esta secção assume que já tem instalado uma imagem RHEL (a partir de um ficheiro de ISO obtidos a partir do Web site do vermelho chapéu) para um disco rígido virtual (VHD). Para obter mais detalhes sobre como utilizar o Gestor de Hyper-V para instalar uma imagem de sistema operativo, consulte o artigo [instalar o função Hyper-V e configurar uma Máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação RHEL**

- Consulte o artigo também [Notas de instalação Linux gerais](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obter mais sugestões sobre como preparar Linux para Azure.

- O formato mais recente do VHDX não é suportado no Azure. Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o cmdlet do PowerShell **vhd converter** .

- VHDs têm de ser criados como "fixo" – dinâmicos VHDs não são suportados.

- Quando estiver a instalar o sistema Linux, recomendamos que utilize a partições padrão em vez de LVM (muitas vezes a predefinição para muitas instalações). Isto irá evitar LVM nome está em conflito com clonado VMs, sobretudo, se um disco SO nunca tem de ser anexado a outra VM para resolução de problemas. LVM ou [RAID](virtual-machines-linux-configure-raid.md) pode ser utilizados em discos dados se preferido.

- Não a configurar uma partição de trocar no disco SO. Pode configurar o agente Linux para criar um ficheiro de trocar no disco recurso temporário. Mais informações sobre esta estão disponíveis nos passos abaixo.

- Todos os VHDs têm de ter os tamanhos que são múltiplos de 1 MB.

- Quando utiliza **qemu img** para converter imagens de disco VHD formato, tenha em atenção que existe um erro conhecido nas versões qemu img 2.2.1 ou posterior. Este erro pode produzir um VHD indevidamente formatado. O problema se destina a ser fixo numa próxima versão do qemu img. Por agora, recomendamos que utilize qemu-img versão 2.2.0 ou anterior.

### <a id="rhel67hyperv"> </a>Preparar uma máquina de virtual RHEL 6,7 do Gestor de Hyper-V###


1.  No Gestor de Hyper-V, selecione a máquina virtual.

2.  Clique em **Ligar** para abrir uma janela de consola para a máquina virtual.

3.  Desinstale NetworkManager executando o seguinte comando:

        # sudo rpm -e --nodeps NetworkManager

    Tenha em atenção que, se o pacote já não estiver instalado, este comando irá falhar com uma mensagem de erro. Isto é esperado.

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

6.  Mover (ou remover) udev regras para evitar gerar estáticas regras para a interface Ethernet. Estas regras causam problemas quando clonar uma máquina virtual no Microsoft Azure ou Hyper-v:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Certifique-se de que o serviço de rede será iniciado durante o arranque, executando o seguinte comando:

        # sudo chkconfig network on

8.  Registe a sua subscrição de chapéu vermelho para ativar a instalação de pacotes do repositório de RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.  O pacote de WALinuxAgent `WALinuxAgent-<version>` foi premido para o repositório de extras chapéu vermelho. Ative o repositório extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10. Modificar a linha de arranque kernel na sua configuração larvas para incluir os parâmetros de kernel adicionais do Azure. Para executar esta tarefa, abra `/boot/grub/menu.lst` num editor de texto e certifique-se de que o kernel predefinido inclui os seguintes parâmetros:

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Isto também irá assegurar que todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Isto irá desactivar an devido a um erro na versão kernel que é utilizado pelo RHEL 6.

    Para além da ação acima, recomendamos que o remova os seguintes parâmetros:

        rhgb quiet crashkernel=auto

    Arranque gráfica e sossegada, não são úteis num ambiente nuvem onde queremos todos os registos sejam enviadas para a porta série.

    A opção crashkernel pode ser esquerda configurado se pretender, mas a nota que este parâmetro irá reduzir a quantidade de memória disponível na VM por 128 MB ou superior. Isto pode ser problemático em tamanhos de VM mais pequenos.

11. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado durante o arranque. Normalmente, esta é a predefinição. Modificar /etc/ssh/sshd_config para incluir a linha seguinte:

        ClientAliveInterval 180

12. Instale o agente de Linux Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    Note que instalar o pacote de WALinuxAgent removerá o NetworkManager e pacotes NetworkManager Gnomo caso já não foram removidas, tal como descrito no passo 2.

13. Não crie trocar espaço no disco SO.
O agente de Linux Azure pode configurar automaticamente o espaço de trocar utilizando o disco de recursos locais é anexado à VM depois da VM está aprovisionada no Azure. Repare que o disco de recursos locais é um disco temporário e pode ser esvaziado quando a VM será descontinuada. Depois de instalar o agente de Linux Azure (consulte o passo anterior), modificar seguintes parâmetros na /etc/waagent.conf corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Anular o registo a subscrição (se necessário), executando o seguinte comando:

        # sudo subscription-manager unregister

15. Execute os seguintes comandos deprovision máquina virtual e prepará-lo para aprovisionar no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. Clique em **ação > encerrar** no Gestor de Hyper-V. O VHD Linux está agora pronto para ser carregado para Azure.
 

### <a id="rhel7xhyperv"> </a>Preparar uma máquina de virtual RHEL 7.1/7.2 do Gestor de Hyper-V###

1.  No Gestor de Hyper-V, selecione a máquina virtual.

2.  Clique em **Ligar** para abrir uma janela de consola para a máquina virtual.

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

5.  Certifique-se de que o serviço de rede será iniciado durante o arranque, executando o seguinte comando:

        # sudo chkconfig network on

6.  Registe a sua subscrição de chapéu vermelho para ativar a instalação de pacotes do repositório de RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  Modificar a linha de arranque kernel na sua configuração larvas para incluir os parâmetros de kernel adicionais do Azure. Para executar esta tarefa, abra `/etc/default/grub` num editor de texto e editar o parâmetro **GRUB_CMDLINE_LINUX** . Por exemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Isto também irá assegurar que todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Para além da ação acima, recomendamos que o remova os seguintes parâmetros:

        rhgb quiet crashkernel=auto

    Arranque gráfica e sossegada, não são úteis num ambiente nuvem onde queremos todos os registos sejam enviadas para a porta série.
    A opção crashkernel pode ser esquerda configurado se pretender, mas a nota que este parâmetro irá reduzir a quantidade de memória disponível na VM por 128 MB ou superior. Isto pode ser problemático em tamanhos de VM mais pequenos.

8.  Depois de terminar edição `/etc/default/grub`, execute o seguinte comando para recriar a configuração de larvas:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.  Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado durante o arranque. Normalmente, esta é a predefinição. Modificar `/etc/ssh/sshd_config` para incluir a linha seguinte:

        ClientAliveInterval 180

10. O pacote de WALinuxAgent `WALinuxAgent-<version>` foi premido para o repositório de extras chapéu vermelho. Ative o repositório extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11. Instale o agente de Linux Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

12. Não crie trocar espaço no disco SO. O agente de Linux Azure pode configurar automaticamente o espaço de trocar utilizando o disco de recursos locais é anexado à VM depois da VM está aprovisionada no Azure. Repare que o disco de recursos locais é um disco temporário e pode ser esvaziado quando a VM será descontinuada. Depois de instalar o agente de Linux Azure (consulte o passo anterior), modificar os seguintes parâmetros na `/etc/waagent.conf` corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Se pretender anular o registo a subscrição, execute o seguinte comando:

        # sudo subscription-manager unregister

14. Execute os seguintes comandos deprovision máquina virtual e prepará-lo para aprovisionar no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Clique em **ação > encerrar** no Gestor de Hyper-V. O VHD Linux está agora pronto para ser carregado para Azure.
 


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Preparar uma máquina de virtual com base em vermelho chapéu de KVM

### <a id="rhel67kvm"> </a>Preparar uma máquina de virtual RHEL 6,7 a partir de KVM###


1.  Transfira a imagem KVM do RHEL 6,7 a partir do website do chapéu vermelho.

2.  Defina uma palavra-passe de raiz.

    Gerar uma palavra-passe encriptada e copie o resultado do comando:

        # openssl passwd -1 changeme

    Defina uma palavra-passe de raiz com guestfish:

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Alterar o segundo campo do utilizador raiz de "!!" para a palavra-passe encriptada.

3.  Criar uma máquina virtual no KVM da imagem de qcow2, defina o tipo do disco para **qcow2**e configurar o modelo de dispositivo de interface de rede virtual para **virtio**. Em seguida, iniciar a máquina virtual e inicie sessão como raiz.

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

6.  Mover (ou remover) as regras de udev para evitar gerar estáticas regras para a interface Ethernet. Estas regras causam problemas quando clonar uma máquina virtual no Microsoft Azure ou Hyper-v:

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Certifique-se de que o serviço de rede será iniciado durante o arranque, executando o seguinte comando:

        # chkconfig network on

8.  Registe a sua subscrição de chapéu vermelho para ativar a instalação de pacotes do repositório de RHEL executando o seguinte comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9.  Modificar a linha de arranque kernel na sua configuração larvas para incluir os parâmetros de kernel adicionais do Azure. Para executar esta tarefa, abra `/boot/grub/menu.lst` num editor de texto e certifique-se de que o kernel predefinido inclui os seguintes parâmetros:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Isto também irá assegurar que todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Isto irá desactivar an devido a um erro na versão kernel que é utilizado pelo RHEL 6.

    Para além da ação acima, recomendamos que o remova os seguintes parâmetros:

        rhgb quiet crashkernel=auto

    Arranque gráfica e sossegada, não são úteis num ambiente nuvem onde queremos todos os registos sejam enviadas para a porta série.
    A opção crashkernel poderá estar esquerda configurado se pretender, mas a nota que este parâmetro irá reduzir a quantidade de memória disponível na VM pelo 128 MB ou superior. Isto pode ser problemático em tamanhos de VM mais pequenos.

10. Adicione módulos Hyper-V para initramfs:  

    Editar `/etc/dracut.conf` e adicionar conteúdo: add_drivers + = "hv_vmbus hv_netvsc hv_storvsc"

    Reconstruir initramfs: # dracut – f - v

11. Desinstale a inicialização da nuvem:

        # yum remove cloud-init

12. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado durante o arranque:

        # chkconfig sshd on

    Modificar /etc/ssh/sshd_config para incluir as linhas seguintes:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Reinicie sshd:

        # service sshd restart

13. O pacote de WALinuxAgent `WALinuxAgent-<version>` foi premido para o repositório de extras chapéu vermelho. Ative o repositório extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14. Instale o agente de Linux Azure executando o seguinte comando:

        # yum install WALinuxAgent
        # chkconfig waagent on

15. O agente de Linux Azure pode configurar automaticamente o espaço de trocar utilizando o disco de recursos locais é anexado à VM depois da VM está aprovisionada no Azure. Repare que o disco de recursos locais é um disco temporário e pode ser esvaziado quando a VM será descontinuada. Depois de instalar o agente de Linux Azure (consulte o passo anterior), modificar seguintes parâmetros na **/etc/waagent.conf** corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Anular o registo a subscrição (se necessário), executando o seguinte comando:

        # subscription-manager unregister

17. Execute os seguintes comandos deprovision máquina virtual e prepará-lo para aprovisionar no Azure:

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18. Encerre a VM no KVM.

19. Converta a imagem qcow2 formato VHD.
    Converta primeiro a imagem para formato não processado:

         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    Certifique-se de que o tamanho da imagem não processado está alinhado com 1 MB. Caso contrário, arredondar para cima o tamanho para alinhar com 1 MB: # MB = $((1024*1024)) tamanho # = $(qemu img informações -f observou – saída json "rhel-6.7.raw" | \ gawk ' corresponder ($0, / "virtual tamanho": ([0-9] +), /, val) {imprimir val[1]}') # rounded_size = $((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    Converta o disco não processado um VHD dimensão fixa:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xkvm"> </a>Preparar uma máquina de virtual RHEL 7.1/7.2 a partir de KVM###


1.  Transfira a imagem KVM do RHEL 7.1 (ou 7.2) a partir do Web site da chapéu vermelho. Irá utilizamos RHEL 7.1 como no exemplo aqui.

2.  Defina uma palavra-passe de raiz.

    Gerar uma palavra-passe encriptada e copiar o resultado do comando:

        # openssl passwd -1 changeme

    Defina uma palavra-passe de raiz com guestfish.

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Alterar o segundo campo do utilizador de raiz de "!!" para a palavra-passe encriptada.

3.  Criar uma máquina virtual no KVM da imagem de qcow2, defina o tipo do disco para **qcow2**e configurar o modelo de dispositivo de interface de rede virtual para **virtio**. Em seguida, iniciar a máquina virtual e inicie sessão como raiz.

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

6.  Certifique-se de que o serviço de rede será iniciado durante o arranque, executando o seguinte comando:

        # chkconfig network on

7.  Registe-se a sua subscrição vermelho chapéu activar a instalação de pacotes do repositório de RHEL executando o seguinte comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8.  Modificar a linha de arranque kernel na sua configuração larvas para incluir os parâmetros de kernel adicionais do Azure. Para executar esta tarefa, abra `/etc/default/grub` num editor de texto e editar o parâmetro **GRUB_CMDLINE_LINUX** . Por exemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Isto também irá assegurar que todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Para além da ação acima, recomendamos que o remova os seguintes parâmetros:

        rhgb quiet crashkernel=auto

    Arranque gráfica e sossegada, não são úteis num ambiente nuvem onde queremos todos os registos sejam enviadas para a porta série.
    A opção crashkernel pode ser esquerda configurado se pretender, mas a nota que este parâmetro irá reduzir a quantidade de memória disponível na VM por 128 MB ou superior. Isto pode ser problemático em tamanhos de VM mais pequenos.

9.  Depois de terminar edição `/etc/default/grub`, execute o seguinte comando para recriar a configuração de larvas:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. Adicione módulos Hyper-V para initramfs:

    Editar `/etc/dracut.conf` e adicionar conteúdo:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Reconstrua initramfs:

        # dracut –f -v

11. Desinstale a inicialização da nuvem:

        # yum remove cloud-init

12. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado durante o arranque:

        # systemctl enable sshd

    Modificar /etc/ssh/sshd_config para incluir as linhas seguintes:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Reinicie sshd:

        systemctl restart sshd

13. O pacote de WALinuxAgent `WALinuxAgent-<version>` foi premido para o repositório de extras chapéu vermelho. Ative o repositório extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14. Instale o agente de Linux Azure executando o seguinte comando:

        # yum install WALinuxAgent

    Ativar o serviço de waagent:

        # systemctl enable waagent.service

15. Não crie trocar espaço no disco SO. O agente de Linux Azure pode configurar automaticamente o espaço de trocar utilizando o disco de recursos locais é anexado à VM depois da VM está aprovisionada no Azure. Repare que o disco de recursos locais é um disco temporário e pode ser esvaziado quando a VM será descontinuada. Depois de instalar o agente de Linux Azure (consulte o passo anterior), modificar os seguintes parâmetros na `/etc/waagent.conf` corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Anular o registo a subscrição (se necessário), executando o seguinte comando:

        # subscription-manager unregister

17. Execute os seguintes comandos deprovision máquina virtual e prepará-lo para aprovisionar no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18. Encerre máquina virtual na KVM.

19. Converta a imagem qcow2 formato VHD.

    Converta primeiro a imagem para formato não processado:

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    Certifique-se de que o tamanho da imagem não processado está alinhado com 1 MB. Caso contrário, arredonde para cima o tamanho para alinhar com 1 MB:

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    Converta o disco não processado um VHD dimensão fixa:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Preparar uma máquina de virtual com base em vermelho chapéu de VMware
### <a name="prerequisites"></a>Pré-requisitos
Esta secção assume que já tem instalado uma máquina de virtual RHEL no VMware. Para obter detalhes sobre como instalar num sistema operativo no VMware, consulte o artigo [Guia de instalação do VMware convidado sistema operativo](http://partnerweb.vmware.com/GOSIG/home.html).

- Quando instala o sistema operativo Linux, recomendamos que utilize a partições padrão em vez de LVM (muitas vezes a predefinição para muitas instalações). Isto irá evitar LVM nome está em conflito com clonado VMs, sobretudo, se um disco SO nunca tem de ser anexado a outra VM para resolução de problemas. LVM ou RAID pode ser utilizado em discos de dados se preferido.

- Não a configurar uma partição de trocar no disco SO. Pode configurar o agente Linux para criar um ficheiro de trocar no disco recurso temporário. Pode encontrar mais informações sobre esta nos passos abaixo.

- Quando cria o disco rígido virtual, selecione **disco virtual do arquivo como um único ficheiro**.



### <a id="rhel67vmware"> </a>Preparar uma máquina de virtual RHEL 6,7 a partir de VMware###

1.  Desinstale NetworkManager executando o seguinte comando:

         # sudo rpm -e --nodeps NetworkManager

    Tenha em atenção que, se o pacote já não estiver instalado, este comando irá falhar com uma mensagem de erro. Isto é esperado.

2.  Crie um ficheiro com o nome de **rede** no /etc/sysconfig/directório que contém o seguinte texto:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.  Criar um ficheiro com o nome **ifcfg eth0** nas /etc/sysconfig/network-scripts / directory que contém o seguinte texto:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.  Mover (ou remover) as regras de udev para evitar gerar estáticas regras para a interface Ethernet. Estas regras causam problemas quando clonar uma máquina virtual no Microsoft Azure ou Hyper-v:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.  Certifique-se de que o serviço de rede será iniciado durante o arranque, executando o seguinte comando:

        # sudo chkconfig network on

6.  Registe a sua subscrição de chapéu vermelho para ativar a instalação de pacotes do repositório de RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  O pacote de WALinuxAgent `WALinuxAgent-<version>` foi premido para o repositório de extras chapéu vermelho. Ative o repositório extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8.  Modificar a linha de arranque kernel na sua configuração larvas para incluir os parâmetros de kernel adicionais do Azure. Para executar esta tarefa, abra "/ boot/grub/menu.lst" num editor de texto e certifique-se de que o kernel predefinido inclui os seguintes parâmetros:

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Isto também irá assegurar que todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Isto irá desactivar an devido a um erro na versão kernel que é utilizado pelo RHEL 6.
    Para além da ação acima, recomendamos que o remova os seguintes parâmetros:

        rhgb quiet crashkernel=auto

    Arranque gráfica e sossegada, não são úteis num ambiente nuvem onde queremos todos os registos sejam enviadas para a porta série.
    A opção crashkernel pode ser esquerda configurado se pretender, mas a nota que este parâmetro irá reduzir a quantidade de memória disponível na VM por 128 MB ou superior. Isto pode ser problemático em tamanhos de VM mais pequenos.

9.  Adicione módulos Hyper-V para initramfs:

        Edit `/etc/dracut.conf` and add content:

            add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

        Rebuild initramfs:

            # dracut –f -v

10. Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado durante o arranque. Normalmente, esta é a predefinição. Modificar `/etc/ssh/sshd_config` para incluir a linha seguinte:

        ClientAliveInterval 180

11. Instale o agente de Linux Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

12. Não crie trocar espaço no disco do SO:

    O agente de Linux Azure pode configurar automaticamente o espaço de trocar utilizando o disco de recursos locais é anexado à VM depois da VM está aprovisionada no Azure. Repare que o disco de recursos locais é um disco temporário e pode ser esvaziado quando a VM será descontinuada. Depois de instalar o agente de Linux Azure (consulte o passo anterior), modificar os seguintes parâmetros na `/etc/waagent.conf` corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Anular o registo a subscrição (se necessário), executando o seguinte comando:

        # sudo subscription-manager unregister

14. Execute os seguintes comandos deprovision máquina virtual e prepará-lo para aprovisionar no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Encerrar a VM e converter o ficheiro VMDK para um ficheiro. vhd.

    Converta primeiro a imagem para formato não processado:

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    Certifique-se de que o tamanho da imagem não processado está alinhado com 1 MB. Caso contrário, arredonde para cima o tamanho para alinhar com 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    Converta o disco não processado um VHD dimensão fixa:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xvmware"> </a>Preparar uma máquina de virtual RHEL 7.1/7.2 a partir de VMware###

1.  Crie um ficheiro com o nome de **rede** no /etc/sysconfig/directório que contém o seguinte texto:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.  Criar um ficheiro com o nome **ifcfg eth0** nas /etc/sysconfig/network-scripts / directory que contém o seguinte texto:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.  Certifique-se de que o serviço de rede será iniciado durante o arranque, executando o seguinte comando:

        # sudo chkconfig network on

4.  Registe a sua subscrição de chapéu vermelho para ativar a instalação de pacotes do repositório de RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.  Modificar a linha de arranque kernel na sua configuração larvas para incluir os parâmetros de kernel adicionais do Azure. Para executar esta tarefa, abra `/etc/default/grub` num editor de texto e editar o parâmetro **GRUB_CMDLINE_LINUX** . Por exemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Isto também irá assegurar que todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Para além da ação acima, recomendamos que o remova os seguintes parâmetros:

        rhgb quiet crashkernel=auto

    Arranque gráfica e sossegada, não são úteis num ambiente nuvem onde queremos todos os registos sejam enviadas para a porta série.
    A opção crashkernel pode ser esquerda configurado se pretender, mas a nota que este parâmetro irá reduzir a quantidade de memória disponível na VM por 128 MB ou superior. Isto pode ser problemático em tamanhos de VM mais pequenos.

6.  Depois de terminar edição `/etc/default/grub`, execute o seguinte comando para recriar a configuração de larvas:

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.  Adicione módulos Hyper-V para initramfs:

    Editar `/etc/dracut.conf`, adicionar conteúdo:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Reconstrua initramfs:

        # dracut –f -v

8.  Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado durante o arranque. Normalmente, esta é a predefinição. Modificar `/etc/ssh/sshd_config` para incluir a linha seguinte:

        ClientAliveInterval 180

9.  O pacote de WALinuxAgent `WALinuxAgent-<version>` foi premido para o repositório de extras chapéu vermelho. Ative o repositório extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10. Instale o agente de Linux Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11. Não crie trocar espaço no disco SO. O agente de Linux Azure pode configurar automaticamente o espaço de trocar utilizando o disco de recursos locais é anexado à VM depois da VM está aprovisionada no Azure. Repare que o disco de recursos locais é um disco temporário e pode ser esvaziado quando a VM será descontinuada. Depois de instalar o agente de Linux Azure (consulte o passo anterior), modificar os seguintes parâmetros na `/etc/waagent.conf` corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. Se pretender anular o registo a subscrição, execute o seguinte comando:

        # sudo subscription-manager unregister

13. Execute os seguintes comandos deprovision máquina virtual e prepará-lo para aprovisionar no Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Encerrar a VM e converta o ficheiro VMDK formato VHD.

    Converta primeiro a imagem para formato não processado:

        # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw

    Certifique-se de que o tamanho da imagem não processado está alinhado com 1 MB. Caso contrário, arredonde para cima o tamanho para alinhar com 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    Converta o disco não processado um VHD dimensão fixa:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Preparar uma máquina de virtual com base em vermelho chapéu a partir de um ISO utilizando um ficheiro de kickstart automaticamente


### <a id="rhel7xkickstart"> </a>Preparar uma máquina de virtual RHEL 7.1/7.2 a partir de um ficheiro de kickstart###


1.  Criar um ficheiro de kickstart com o conteúdo abaixo e guarde o ficheiro. Para obter detalhes sobre a instalação kickstart, consulte o [Guia de instalação do Kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).



        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff



        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2.  Coloque o ficheiro kickstart num local que esteja acessível a partir do sistema de instalação.

3.  No Gestor de Hyper-V, crie uma nova VM. Na página **Ligar Virtual no disco rígido** , selecione **anexar um disco rígido virtual mais tarde**e concluir o Assistente de Máquina Virtual novo.

4.  Abra as definições de VM:

    um.  Anexe um disco rígido virtual novo para a VM. Certifique-se selecionar **VHD formato** e **Tamanho fixo**.

    b.  Anexe a instalação ISO a unidade de DVD.

    c.  Defina o BIOS para arrancar a partir de CD.

5.  Comece a VM. Quando o guia de instalação for apresentada, prima a tecla de **tabulação** para configurar as opções de arranque.

6.  ENTER `inst.ks=<the location of the kickstart file>` no final das opções de arranque e prima **Enter**.

7.  Aguarde que a instalação concluir. Quando terminar, a VM será automaticamente encerrada. O VHD Linux está agora pronto para ser carregado para Azure.

## <a name="known-issues"></a>Problemas conhecidos
Existem problemas conhecidos quando estiver a utilizar RHEL 7.1 no Hyper-V e Azure.

### <a name="disk-io-freeze"></a>Fixar do disco e/s

Este problema pode ocorrer durante atividades e/s de disco de armazenamento frequente com RHEL 7.1 no Hyper-V e Azure.   

Taxa de reproduzível:

Este problema for intermitente. No entanto, ocorre mais frequentemente durante frequentam operações do disco e/s no Hyper-V e Azure.   


[AZURE.NOTE] Este problema conhecido já foi resolvido pelo chapéu vermelho. Para instalar as correções associadas, execute o seguinte comando:

    # sudo yum update

### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>O controlador de Hyper-V não foi possível será incluído no disco de RAM inicial ao utilizar um hipervisor não Hyper-V

Em alguns casos, Linux programas de instalação podem não incluir os controladores para o Hyper-V no disco de RAM inicial (initrd ou initramfs), a menos que detetar que está em execução num ambiente Hyper-V.

Quando estiver a utilizar um sistema de Virtualização diferente (ou seja, Virtualbox, Xen, etc.) para preparar a sua imagem Linux, poderá ter de a reconstruir initrd para se certificar de que pelo menos os módulos de kernel hv_vmbus e hv_storvsc estão disponíveis no disco RAM inicial. Este é um problema conhecido pelo menos nos sistemas com base na distribuição vermelho chapéu montante.

Para resolver este problema, terá de adicionar módulos Hyper-V para initramfs e reconstruir:

Editar `/etc/dracut.conf` e adicionar conteúdo:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

Reconstrua initramfs:

        # dracut –f -v

Para obter mais detalhes, consulte as informações sobre [a reformulação repetida de initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Próximos passos
Agora está pronto para utilizar o seu disco rígido virtual vermelho chapéu Enterprise Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para Azure, consulte os passos 2 e 3 em [criar e carregar um disco rígido virtual que contém o sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).

Para obter mais detalhes sobre a hypervisors que tenham certificação para executar vermelho chapéu Enterprise Linux, consulte [o Web site chapéu vermelho](https://access.redhat.com/certified-hypervisors).
