<properties
    pageTitle="Criar e carregar um VHD Linux no Azure"
    description="Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operativo do Linux."
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
    ms.date="09/23/2016"
    ms.author="szark"/>

# <a name="information-for-non-endorsed-distributions"></a>Informações para não menção distribuições #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


**Importante**: Azure a plataforma SLA aplica-se a máquinas virtuais a executar o sistema operativo Linux apenas quando uma das [menção distribuições](virtual-machines-linux-endorsed-distros.md) é utilizada. Todas as distribuições Linux que são fornecidas na Galeria de imagem Azure são as distribuições menção com a configuração necessária.

- [Linux no Azure - menção distribuições](virtual-machines-linux-endorsed-distros.md)
- [Suporte para Linux imagens no Microsoft Azure](https://support.microsoft.com/kb/2941892)

Todas as distribuições em execução no Azure serão necessário satisfazer um número de pré-requisitos para ter a oportunidade de corretamente executar a plataforma.  Este artigo por não significa é exaustiva cada distribuição é diferente; e é muito provável que mesmo se cumprir os critérios abaixo ainda será necessário aperfeiçoar significativamente o seu sistema de Linux para se certificar de que é executado corretamente a plataforma.

É por este motivo que recomendamos que começa com um dos nossos [Linux no Azure menção distribuições](virtual-machines-linux-endorsed-distros.md) sempre que possível. Os seguintes artigos irão guiá-lo através de como preparar as vários menção Linux as distribuições que são suportadas no Azure:

- **[Com base em centOS distribuições](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Chapéu vermelho Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**

Os restantes este artigo irão focar informações gerais para executar a sua distribuição Linux num Azure.


## <a name="general-linux-installation-notes"></a>Notas de instalação Linux gerais ##

- O formato VHDX não é suportado no Azure, apenas **corrigido VHD**.  Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o cmdlet vhd converter. Se estiver a utilizar VirtualBox Isto significa que selecionar **tamanho fixo** por oposição a predefinição dinamicamente atribuída ao criar o disco.

- Ao instalar o sistema Linux é *recomendado* que utiliza a partições padrão em vez de LVM (muitas vezes a predefinição para muitas instalações). Isto irá evitar LVM nome está em conflito com clonado VMs, sobretudo, se um disco SO nunca tem de ser anexado a outra VM idêntico para resolução de problemas. [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) pode ser utilizados em discos de dados.

- Suporte de kernel para montagem sistemas de ficheiros UDF é necessário. No primeiro arranque no Azure a configuração de aprovisionamento lhe é transmitida a VM Linux através de multimédia formatados como UDF que está anexada ao convidado. O agente do Azure Linux tem de conseguir montagem o sistema de ficheiros UDF para ler a sua configuração e aprovisionar a VM.

- Versões de kernel Linux abaixo 2.6.37 não suportam an no Hyper-V com tamanhos VM maiores. Este problema principalmente impactos distribuições mais antigas, utilizando o montante kernel chapéu vermelho 2.6.32 e foi fixo na RHEL 6.6 (kernel-2.6.32 504). Sistemas que executem o kernels personalizados mais antigas do que 2.6.37 ou com base em RHEL kernels mais antigas do que 2.6.32-504 tem de definir o parâmetro de arranque `numa=off` sobre o kernel da linha de comandos no grub.conf. Para obter mais informações consulte o artigo vermelho chapéu [KB 436883](https://access.redhat.com/solutions/436883).

- Não a configurar uma partição de trocar no disco SO. O agente de Linux pode ser configurado para criar um ficheiro de trocar no disco recurso temporário.  Podem encontrar mais informações sobre esta nos passos abaixo.

- Todos os VHDs têm de ter os tamanhos que são múltiplos de 1 MB.


### <a name="installing-linux-without-hyper-v"></a>Instalar o Linux sem Hyper-V ###

Em alguns casos, Linux programas de instalação poderão não incluir os controladores para o Hyper-V no inicial o disco de RAM (initrd ou initramfs), a menos que detetar que está a ser executado num ambiente Hyper-V.  Ao utilizar um sistema de Virtualização diferente (ou seja, Virtualbox, KVM, etc.) para preparar a sua imagem Linux, poderá ter de a reconstruir initrd para se certificar de que pelo menos o `hv_vmbus` e `hv_storvsc` módulos kernel estão disponíveis no disco de RAM inicial.  Este é um problema conhecido pelo menos nos sistemas com base na distribuição vermelho chapéu montante.

Dispositivo de a reconstruir a imagem initrd ou initramfs pode variar dependendo da distribuição. Fórum da documentação do seu distribuição ou de suporte para o procedimento inicial maiúscula.  Eis um exemplo como reconstruir a initrd utilizando o `mkinitrd` utility:

Em primeiro lugar, criar cópias de segurança a imagem initrd existente:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Em seguida, recriar initrd com o `hv_vmbus` e `hv_storvsc` módulos kernel:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Redimensionar VHDs ###

Imagens VHD no Azure tem de ter um tamanho virtual alinhado 1MB.  Normalmente, VHDs criados utilizando o Hyper-V devem já ser alinhados corretamente.  Se o VHD não estiver alinhado corretamente poderá receber uma mensagem de erro semelhante ao seguinte quando tenta criar uma *imagem* a partir do seu VHD:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Para resolver este problema, pode redimensionar a VM utilizando a consola do Gestor de Hyper-V ou o cmdlet do Powershell [VHD de redimensionamento](http://technet.microsoft.com/library/hh848535.aspx) .  Se não estiver a executar num ambiente do Windows, em seguida, é recomendado utilizar qemu img para converter (se necessário) e redimensionar o VHD.

> [AZURE.NOTE] Existe um erro conhecido nas versões qemu img > = 2.2.1 que resulta um VHD indevidamente formatado. O problema foi corrigido no QEMU 2.6. É aconselhável utilizar qemu-img 2.2.0 ou inferior, ou atualizar para 2.6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.


 1. Redimensionar o VHD diretamente com ferramentas tal como `qemu-img` ou `vbox-manage` pode resultar num VHD de arranque.  Por isso, recomenda-se a primeira converter o VHD a uma imagem do disco bruto.  Se a imagem VM já foi criada como imagem de disco bruto (a predefinição para alguns Hypervisors como KVM), em seguida, pode ignorar este passo:

        # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

 2. Calcule o tamanho da imagem para se certificar de que o tamanho virtual está alinhado para 1MB disco necessário.  O seguinte script de shell de festa pode ajudar com o seguinte.  Utiliza o script "`qemu-img info`" para determinar o tamanho virtual da imagem no disco e, em seguida, calcula o tamanho para o próximo 1MB:

        rawdisk="MyLinuxVM.raw"
        vhddisk="MyLinuxVM.vhd"

        MB=$((1024*1024))
        size=$(qemu-img info -f raw --output json "$rawdisk" | \
               gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        rounded_size=$((($size/$MB + 1)*$MB))
        echo "Rounded Size = $rounded_size"

 3. Redimensione o disco não processado utilizando $rounded_size conforme definido no script acima:

        # qemu-img resize MyLinuxVM.raw $rounded_size

 4. Agora, o disco bruto voltar a converter um tamanho fixo VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd



## <a name="linux-kernel-requirements"></a>Requisitos de Linux Kernel ##

Os controladores de lista Linux Integration Services (vertical) para Hyper-V e Azure são contribuiu diretamente para o montante kernel de Linux. Muitos distribuições que incluem uma versão recente Linux kernel (ou seja, 3. x) serão já tenho estes controladores disponíveis ou, caso contrário, fornecer versões backported destes controladores com os respetivos kernels.  Estes controladores são constantemente a ser actualizados no kernel do montante com correcções novas funcionalidades e, para que sempre que possível recomenda-se para executar uma [menção distribuição](virtual-machines-linux-endorsed-distros.md) que irá incluir estas atualizações e correções.

Se estiver a executar uma variante do vermelho chapéu Enterprise Linux versões **6.0 6.3**, terá de instalar os controladores mais recentes da lista de vertical para Hyper-V. Os controladores podem ser encontrados [em desta localização](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Partir RHEL **6.4 +** (e derivados) os controladores de lista vertical já estão incluídos no kernel e por isso, não existem pacotes de instalação adicionais são necessários para executar esses sistemas no Azure.

Se for necessário um kernel personalizado, recomenda-se para utilizar uma versão mais recente do kernel (ou seja, **3.8 +**). Para esses distribuições ou os fornecedores mantêm os seus próprios kernel, algumas esforço será necessário para regularmente backport os controladores de lista vertical do montante núcleo para sua kernel personalizado.  Mesmo se já estiver a executar uma versão de kernel relativamente recente, recomenda-se vivamente para controlar quaisquer correções montante na controladores de lista vertical e backport aqueles conforme necessário. A localização dos ficheiros de origem do controlador de lista vertical está disponível no ficheiro [EXPAT](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) na árvore de origem do Linux kernel:

    F:  arch/x86/include/asm/mshyperv.h
    F:  arch/x86/include/uapi/asm/hyperv.h
    F:  arch/x86/kernel/cpu/mshyperv.c
    F:  drivers/hid/hid-hyperv.c
    F:  drivers/hv/
    F:  drivers/input/serio/hyperv-keyboard.c
    F:  drivers/net/hyperv/
    F:  drivers/scsi/storvsc_drv.c
    F:  drivers/video/fbdev/hyperv_fb.c
    F:  include/linux/hyperv.h
    F:  tools/hv/

Na linha um muito mínimo, ausência de seguintes patches ter sido conhecidas por causar problemas no Azure e por isso, estes devem ser incluídos nos kernel. Esta lista por não significa é exaustiva ou conclusão de todas as distribuições:

- [ata_piix: diferir discos para os controladores Hyper-V por predefinição](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [miniporta storvsc: para pacotes em trânsito no caminho de reposição da conta](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
- [miniporta storvsc: evitar a utilização da WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
- [miniporta storvsc: Desactivar escrever mesmo para RAID e controladores de placa de sistema anfitrião virtual](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
- [miniporta storvsc: apontador NULL anular fix](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
- [miniporta storvsc: falhas de memória intermédia em simultâneo podem resultar em fixar e/s](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
- [scsi_sysfs: proteger contra dupla execução dos __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)


## <a name="the-azure-linux-agent"></a>O agente Linux Azure ##

O [Azure Linux agente](virtual-machines-linux-agent-user-guide.md) (waagent) é necessária para aprovisionar corretamente uma máquina de virtual Linux no Azure. Pode obter a versão mais recente, problemas de ficheiro ou submeta pedidos de solicitação no [Linux agente GitHub repo](https://github.com/Azure/WALinuxAgent).

- O agente Linux é disponibilizado ao abrigo da licença Apache 2.0. Muitos distribuições já fornecem rotações/minuto ou Debora pacotes do agente de e, por isso, em alguns casos este pode ser instalado e atualizado com pouco esforço.

- O agente de Linux Azure requer Python v2.6 +.

- O agente também requer o módulo python pyasn1. A maior parte dos distribuições fornecem esta informação como um pacote separado que pode ser instalado.

- Em alguns casos o agente de Linux Azure podem não ser compatível com NetworkManager. Muitos dos pacotes rotações/minuto/Debora fornecidos pela distribuições configurar NetworkManager como um conflito para o pacote de waagent e, por conseguinte, irão desinstalar NetworkManager ao instalar o pacote do agente Linux.


## <a name="general-linux-system-requirements"></a>Requisitos de sistema de Linux geral ##

- Modificar a linha de arranque do kernel no LARVAS ou GRUB2 para incluir os seguintes parâmetros. Isto também irá assegurar a todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração:

        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300

    Isto também irá assegurar a todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração.

    Para além de acima, é aconselhável para *Remover* os seguintes parâmetros que existam:

        rhgb quiet crashkernel=auto

    Arranque gráfica e sossegada, não são úteis num ambiente nuvem onde queremos todos os registos sejam enviadas para a porta série.

    O `crashkernel` opção pode ser esquerda configurado se pretender, mas a nota que este parâmetro irá reduzir a quantidade de memória disponível na VM por 128MB ou superior, que podem ser problemático nos tamanhos VM mais pequenos.

- Instalar o agente Linux Azure

    O agente de Linux Azure é necessário para uma imagem Linux Azure de aprovisionamento.  Muitos distribuições fornecem o agente como um pacote de rotações/minuto ou Debora (o pacote é normalmente chamado 'WALinuxAgent' ou 'walinuxagent').  O agente de também pode ser instalado manualmente ao seguir os passos no [Guia de agente Linux](virtual-machines-linux-agent-user-guide.md).

- Certifique-se de que o servidor SSH é instalado e configurado para ser iniciado durante o arranque.  Normalmente, esta é a predefinição.

- Não crie trocar espaço no disco SO

    O agente de Linux Azure pode configurar automaticamente o espaço de trocar utilizando o disco de recursos locais é anexado à VM depois de aprovisionamento no Azure. Repare que o disco de recursos locais é um disco *temporário* e pode ser esvaziado quando a VM será descontinuada. Depois de instalar o agente de Linux Azure (consulte o passo anterior), modificar seguintes parâmetros na /etc/waagent.conf corretamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- Como um passo final, execute os seguintes comandos para deprovision máquina virtual:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

    >[AZURE.NOTE] Em Virtualbox poderá ver a seguinte mensagem de erro depois de executar ' waagent-forçar - deprovision': `[Errno 5] Input/output error`. Esta mensagem de erro não é crítica e pode ser ignorada.

- Em seguida, terá encerrar a máquina virtual e carregue o VHD para Azure.
